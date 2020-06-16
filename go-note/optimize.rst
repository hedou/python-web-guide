.. _optimize:

Go 性能优化
=====================================================================

string 与 []byte 互转
---------------------------------------------------------------
利用了底层 string 和 byte slice 实现的技巧，如果需要大量互转可以使用这种方式。

.. code-block:: go

    /*
    type StringHeader struct {
        Data uintptr
        Len  int
    }
    type SliceHeader struct {
        Data uintptr
        Len  int
        Cap  int
    }
    */

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

字符串拼接
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
