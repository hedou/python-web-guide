.. _optimize:

Go 性能优化
=====================================================================

常见的优化手段
---------------------------------------------------------------
- 池化技术。比如 sync.Pool；协程池(Jeffail/tunny, panjf2000/ants)
- 复用对象。比如 string2bytes
- 优化反射。https://github.com/goccy/go-reflect
- 减小锁消耗。减小锁粒度；使用原子操作(atomic)代替互斥锁。读多写少的场景使用读写锁(RWMutex)
- 减小锁竞争。比如 go-cache/bigcache 等缓存库都是通过分片功能减少锁竞争

优化建议：

- 预分配内存。slice/map 如果预知容量信息，初始化应该提供，减少内存重分配和复制元素的消耗。 `make([]int, 0, cap); make(map[int]int, cap)`
- 使用 strings.Builder 拼接大量字符串
- 使用空的结构体作为占位符，空结构体 struct{} 不占内存。 比如实现 set 使用 map[string]struct{}{}
- 使用 atomic 代替 sync 包
- struct 合理的布局可以减少内存占用，提升性能(内存对齐)
- 减少逃逸，将变量限制在栈上，减少堆变量分配，降低 GC 成本，提高程序性能

  - 局部切片尽可能确定长度或者容量
  - 函数返回指针可以减少拷贝但是会导致内存分配逃逸到堆中。如果要修改原对象或者返回内存比较大的对象，返回指针。对于只读或
    者占用内存小的对象，可以直接返回值。
  - 如果返回值的类型可以确定，就不要用 interface{}

string 与 []byte 互转
---------------------------------------------------------------
利用了底层 string 和 byte slice 实现的技巧，如果需要大量互转可以使用这种方式。

.. code-block:: go

    /*
    type StringHeader struct { // reflect.StringHeader
        Data uintptr
        Len  int
    }
    type SliceHeader struct { // reflect.SliceHeader
        Data uintptr
        Len  int
        Cap  int
    }
    */

    // NOTE：注意之后不要修改 string
    func str2bytes(s string) []byte {
       x := (*[2]uintptr)(unsafe.Pointer(&s))
       b := [3]uintptr{x[0], x[1], x[1]}
       return *(*[]byte)(unsafe.Pointer(&b))
    }

    func bytes2str(b []byte) string {
       return *(*string)(unsafe.Pointer(&b))
    }


- `Go性能优化技巧 <https://segmentfault.com/a/1190000005006351>`_
- `Golang 中 string 与 []byte 互转优化 <https://medium.com/@kevinbai/golang-%E4%B8%AD-string-%E4%B8%8E-byte-%E4%BA%92%E8%BD%AC%E4%BC%98%E5%8C%96-6651feb4e1f2>`_
- `Go 语言高性能编程 <https://geektutu.com/post/high-performance-go.html>`_

大量字符串拼接
---------------------------------------------------------------
大量字符串拼接不要用 + ，使用 bytes.Buffer 或者 strings.Builder

.. code-block:: go

    // bytes.Buffer
    package main

    import (
        "fmt"
        "bytes"
    )

    func main() {
        var b bytes.Buffer

        b.WriteString("abc")
        b.WriteString("def")

        fmt.Println(b.String()) // abcdef
    }

.. code-block:: go

    // strings.Builder
    package main

    import (
        "fmt"
        "strings"
    )

    func main() {
        var sb strings.Builder
        sb.WriteString("First")
        sb.WriteString("Second")
        fmt.Println(sb.String())    // FirstSecond
    }

- `concatenate strings in golang <https://golangdocs.com/concatenate-strings-in-golang>`_
- `How to efficiently concatenate strings in go <https://stackoverflow.com/questions/1760757/how-to-efficiently-concatenate-strings-in-go>`_
