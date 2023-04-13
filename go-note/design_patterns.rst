.. _go_design_patterns:

Go 设计模式
=====================================================================

单例模式(Singleton)
--------------------------------------------------
.. code-block:: go

    package main

    import (
        "fmt"
        "sync"
    )

    /*
    go 单例模式实现方式：

    1. 使用 lock，为了并发安全，使用 lock + double check
    2. 使用 sync.Once (推荐👍)
    3. 使用 init() The init function is only called once per file in a package,  so we can be sure that only a single instance will be created

    参考：

    - https://blog.cyeam.com/designpattern/2015/08/12/singleton
    - https://golangbyexample.com/all-design-patterns-golang/
    - https://medium.com/golang-issue/how-singleton-pattern-works-with-golang-2fdd61cd5a7f
    */

    var lock = &sync.Mutex{}

    type single struct {
    }

    var singleInstance *single

    func getInstance() *single {
         if singleInstance == nil { // 防止每次调用 getInstance 都频繁加锁
             lock.Lock()
             defer lock.Unlock()
             // double check. 如果超过一个goroutine进入这里，保证只有一个 goroutine 创建
             if singleInstance == nil {
                 fmt.Println("Creting Single Instance Now")
                 singleInstance = &single{}
             } else {
                 fmt.Println("Single Instance already created-1")
             }
         } else {
             fmt.Println("Single Instance already created-2")
         }
         return singleInstance
    }

    func main() {
         for i := 0; i < 100; i++ {
             go getInstance()
         }
         // Scanln is similar to Scan, but stops scanning at a newline and
         // after the final item there must be a newline or EOF.
         fmt.Scanln()
    }


.. code-block:: go

    // 推荐使用 once，实现更加简洁优雅
    var once sync.Once

    type single struct {
    }

    var singleInstance *single

    func getInstance() *single {
            if singleInstance == nil {
                    once.Do(func() {
                            fmt.Println("Creting Single Instance Now")
                            singleInstance = &single{}
                    })
            } else {
                    fmt.Println("Single Instance already created-2")
            }
            return singleInstance
    }

参考:
--------------------------------------------------

- `Go Patterns (github) <https://github.com/tmrts/go-patterns>`_
- `All Design Patterns in Go (Golang) <https://golangbyexample.com/singleton-design-pattern-go/>`_
