.. _optimize:

Go 性能优化
=====================================================================

常见的优化手段
---------------------------------------------------------------
- 池化技术。比如 sync.Pool 对象池(频繁分配同一类型的多个对象)；协程池(Jeffail/tunny, panjf2000/ants)；连接池(buraksezer/connpool)；内存池等
- 复用对象。比如 string2bytes
- 优化反射。https://github.com/goccy/go-reflect
- 减小锁消耗。减小锁粒度；使用原子操作(atomic)代替互斥锁。读多写少的场景使用读写锁(RWMutex)
- 减小锁竞争。比如 go-cache/bigcache 等缓存库都是通过分片功能减少锁竞争

优化建议：

- 预分配内存。slice/map 如果预知容量信息，初始化应该提供，减少内存重分配和复制元素的消耗。 ``make([]int, 0, cap); make(map[int]int, cap)``
- 使用 strings.Builder 拼接大量字符串
- 使用空的结构体作为占位符，空结构体 struct{} 不占内存。 比如实现 set 使用 map[string]struct{}{}
- 使用 atomic 代替 sync 包。atomic 包的操作指令级支持
- struct 合理的布局可以减少内存占用，提升性能(内存对齐)。一个简单的规则是按照字节大小倒序排列。
- 尽量减少逃逸，将变量限制在栈上，减少堆变量分配，降低 GC 成本，提高程序性能

  - 局部切片尽可能确定长度或者容量。长度和容量尽可能是一个常量，如果是一个变量go无法判断其大小会逃逸到堆上分配
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
字符串在 go 中是不可变对象。大量字符串(一般超过 5 个字符串)拼接不要用 + ，使用 bytes.Buffer 或者 strings.Builder。
不过对于个数比较少的字符串拼接，直接用 + 效率也很高，比 fmt.Sprintf 更快，所以少量字符串拼接可以放心使用 + 。

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


更快的随机数
---------------------------------------------------------------
Go 内置的 rand.Int()在生成随机数时，为了并发安全底层使用了锁，在高并发常见下会有性能问题。
可以使用 github.com/valyala/fastrand 等三方库替换。


伪共享问题(false sharing)
---------------------------------------------------------------
如果并发更新一个结构体的字段，我们可以通过填充空字节防止字段被 cpu 缓存到一个 cache line 单位中，需要不断同步降低效率。
可以在 https://github.com/uber-go/ratelimit 中找到一个例子：

.. code-block:: go

    type leakyBucketLimiter struct {
        state unsafe.Pointer // 是一个状态的指针，用于存储上一次的执行的时间，以及需要 sleep  的时间

        //lint:ignore U1000 Padding is unused but it is crucial to maintain performance
        // of this rate limiter in case of collocation with other frequently accessed memory.
        padding [56]byte // cache line size - state pointer size = 64 - 8; created to avoid false sharing.(伪共享)
        // cpu cache 一般是以 cache line 为单位的，在 64 位的机器上一般是 64 字节
        // 所以如果我们高频并发访问的数据小于 64 字节的时候就可能会和其他数据一起缓存，其他数据如果出现改变就会导致 cpu 认为缓存失效，这就是 false sharing
        // 所以在这里为了尽可能提高性能，填充了 56 字节的无意义数据，因为 state 是一个指针占用了 8 个字节，所以 64 - 8 = 56

        perRequest time.Duration // perRequest = 1s / rate，每个请求间隔 1s/perRequest
        maxSlack   time.Duration // 松弛时间，也就是可以允许的突发流量的大小，默认是 Pre / 10
    }


正确设置容器 CPU 配额
---------------------------------------------------------------
容器中运行 Go 程序需要正确设置 GOMAXPROCS，推荐使用 https://github.com/uber-go/automaxprocs 这个库，直接一行代码就可以。
``import _ "go.uber.org/automaxprocs"``

