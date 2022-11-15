.. _gotricks:

Go 踩坑
=====================================================================

go初学者常见错误
---------------------------------------------------------------
强烈建议 go 新手先过一遍下边这篇文章，go 写多了你一定会遇到很多里边总结的坑。

- `50 Shades of Go: Traps, Gotchas, and Common Mistakes for New Golang Devs  <http://devs.cloudimmunity.com/gotchas-and-common-mistakes-in-go-golang/>`_
- https://github.com/teivah/100-go-mistakes

Go tricks
--------------------------------------------------

影子变量(Shadowed variables)
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    // https://yourbasic.org/golang/gotcha-shadowing-variables/
    func main() {
        n := 0
        if true {
            n := 1
            n++
        }
        fmt.Println(n) // 0，注意 if 作用与里边使用 := 赋值隐藏了外部的 n，所以原来的 n 打印还是 0
        // 如果想要修改 n，直接用 n = 1
    }

golang cannot refer to unexported field or method
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

小写开头只能被本 package 访问

- https://stackoverflow.com/questions/24487943/invoke-golang-struct-function-gives-cannot-refer-to-unexported-field-or-method

访问其它Go package中的私有函数(不推荐)
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

可以写一个公有函数暴露私有方法，不过一般私有方法都是希望隐藏的，就像 python 的下划线虽然不强制，但是不推荐使用。

- https://colobu.com/2017/05/12/call-private-functions-in-other-packages/

Can't load package
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
同一个 folder 不要存在不同 package，否则 can't load package

package is folder.  package name is folder name.  package path is folder path.

- https://studygolang.com/articles/8312


如何编译期间检查是否实现接口？
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    // for pointers to struct
    type MyType struct{}
    var _ MyInterface = (*MyType)(nil)  // 很多源码中可以看到这种写法，但是一开始感觉有点奇怪
    // for struct literals
    var _ MyInterface = MyType{}
    // for other types - just create some instance
    type MyType string
    var _ MyInterface = MyType("doesn't matter")

- https://splice.com/blog/golang-verify-type-implements-interface-compile-time/
- https://medium.com/@matryer/golang-tip-compile-time-checks-to-ensure-your-type-satisfies-an-interface-c167afed3aae


如何通过反射判断是否实现接口?
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    type CustomError struct{}

    func (*CustomError) Error() string {
        return ""
    }

    func testImplements() {
        // 判断某个类型是否实现了接口。
        // 获取接口类型 reflect.TypeOf((*<interface>)(nil)).Elem()
        typeOfError := reflect.TypeOf((*error)(nil)).Elem()
        customErrorPtr := reflect.TypeOf(&CustomError{})
        customError := reflect.TypeOf(CustomError{})

        fmt.Println(customErrorPtr.Implements(typeOfError)) // true
        fmt.Println(customError.Implements(typeOfError))    // false
    }


Go 运行单个测试文件报错 undefined？
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

执行命令时加入这个测试文件需要引用的源码文件，在命令行后方的文件都会被加载到command-line-arguments中进行编译

`go test -v getinfo_test.go  lib.go`

- https://www.cnblogs.com/Detector/p/10010292.html
- https://stackoverflow.com/questions/14723229/go-test-cant-find-function-in-a-same-package

Go 循环遍历 []struct 是值传递
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
注意循环遍历一个结构体切片是值传递，如果想要修改 struct 的值，请使用 slice 下标赋值或者用结构体指针。

.. code-block:: go

    type Cat struct {
      name string
    }

    func testSliceAssign() {
      cats := []Cat{
        {name: "cat1"},
        {name: "cat2"},
      }
      for _, cat := range cats { // cat 这里是拷贝的值
        cat.name = "new cat"  // NOTE: 注意这里 cat 是拷贝的值，所以你无法修改 cat。使用下标或者指针
      }
      fmt.Println(cats) // 无法修改 [{cat1} {cat2}]

      // 方式1：使用下标
      for i, _ := range cats {
        cats[i].name = "new cat"
      }
      fmt.Println(cats)

      // 方式2：使用struct 指针
      pcats := []*Cat{
        {name: "cat1"},
        {name: "cat2"},
      }
      for _, cat := range pcats {
        cat.name = "new cat"
      }
      for _, cat := range pcats {
        fmt.Println(cat)
      }
    }

Go 无法修改结构体的值
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
注意如果需要修改结构体的值，必须使用指针接收者，否则无法修改结构体的值(值拷贝)。

.. code-block:: go

    type Student struct {
        Name string
    }

    func (s Student) SetName(name string) { // NOTE: 无法修改，接收者是值拷贝
        s.Name = name
    }

    func (s *Student) SetNameByPointer(name string) { // 指针接收者才能修改
        s.Name = name
    }

    func testChangeName() {
        s := &Student{Name: "lao wang"}
        s.SetName("lao li")
        fmt.Printf("%+v\n", s) // NOTE: 输出还是 lao wang，这里是值拷贝修改不了
        s.SetNameByPointer("lao li")
        fmt.Printf("%+v\n", s) // 输出 lao li，修改成功！
    }

Go 无法修改值为结构体的map
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    func testChangeMapStruct() {
        type T struct{ Cnt int }
        m := map[string]T{"a": T{Cnt: 1}, "b": T{Cnt: 2}}
        for _, v := range m {
            v.Cnt = 100
        }
        fmt.Println(m)

        // 修改值为结构体的 map，需要使用指针
        m2 := map[string]*T{"a": &T{Cnt: 1}, "b": &T{Cnt: 2}}
        for _, v := range m2 {
            v.Cnt = 100
        }
        fmt.Println(m2["a"], m2["b"])

        // 或者使用 map[k].v 修改 (也需要value是结构体指针)
        m3 := map[string]*T{"a": &T{Cnt: 1}, "b": &T{Cnt: 2}}
        for k := range m3 {
            m3[k].Cnt = 100
        }
        fmt.Println(m3["a"], m3["b"])
    }

    func main() {
      testChangeMapStruct()
    }

不要并发读写map，可能导致程序崩溃
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
使用内置 map 注意几点:

- 使用 make 初始化。直接声明就赋值会 panic。有时候可能会忘记一些隐含场景，比如：

  - 结构体里有一个 map 成员，直接赋值也会 panic
  - map 作为函数的命名返回值的时候，直接赋值也会 panic

- 赋值是浅拷贝。深拷贝需要自己复制
- 内置 map 不要并发写入或者删除，必须加锁。或者使用 sync.Map

如果多个 goroutine 并发对 map 进行读写，必须要同步，否则可能导致进程退出

.. code-block:: go

    // https://blog.golang.org/go-maps-in-action
    var counter = struct{
        sync.RWMutex
        m map[string]int
    }{m: make(map[string]int)}

    counter.RLock() // locks for reading
    n := counter.m["some_key"]
    counter.RUnlock()
    fmt.Println("some_key:", n)

    counter.Lock() // locks for writing
    counter.m["some_key"]++
    counter.Unlock()

不要拷贝 mutex 对象
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
如果一个结构体包含 mutex 对象，不要 copy 它。比如如果放到了一个结构体里，必须使用指针接收者防止拷贝。

.. code-block:: go

    type Container struct {
        sync.Mutex // <-- Added a mutex
        counters   map[string]int
    }

    func (c Container) inc(name string) { // NOTE: 错误！ 这里必须使用指针接收者，否则会 copy mutex 成员
        c.Lock() // <-- Added locking of the mutex
        defer c.Unlock()
        c.counters[name]++
    }

    func (c *Container) inc(name string) { // 正确写法！ 用指针接收者
        c.Lock() // <-- Added locking of the mutex
        defer c.Unlock()
        c.counters[name]++
    }

如何判断一个空结构体(empty struct)
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    // 注意需要加上括号，否则 syntax error
    // https://stackoverflow.com/questions/28447297/how-to-check-for-an-empty-struct
    if (Session{}) == session  {
        fmt.Println("is zero value")
    }

go 如何实现函数默认值(go本身没提供)
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    // https://stackoverflow.com/questions/19612449/default-value-in-gos-method
    // 可以通过传递零值或者 nil 的方式来判断。
    // Both parameters are optional, use empty string for default value
    func Concat1(a string, b int) string {
      if a == "" {
        a = "default-a"
      }
      if b == 0 {
        b = 5
      }

      return fmt.Sprintf("%s%d", a, b)
    }

    // 或者使用传递结构体的方式，使用结构体的默认零值或者构造函数的初始值
    type Params struct {
      a, b, c int
    }

    func doIt(p Params) int {
      return p.a + p.b + p.c
    }


go 初始化 slice/map 的区别
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
直接看代码，注意 map 赋值之前需要先 make 创建，直接给一个 nil map 赋值会 panic，但是 slice 却可以直接声明然后 append。
如果是一个 struct 包含了 map，你应该在构造函数里进行 make 初始化，否则直接赋值也会 panic。

.. code-block:: go

    // 初始化一个全局 map 可以用 make，防止第一次赋值 nil map 会 panic
    // https://nanxiao.gitbooks.io/golang-101-hacks/content/posts/nil-slice-vs-nil-map.html
    var globalMap = make(map[string]string) // 之后可以在 init() 函数初始化

    func main() {
            var intSlice []int // 注意可以直接声明一个 slice 然后 append
            fmt.Println(intSlice)
            intSlice = append(intSlice, 1)
            fmt.Println(intSlice)

            // 已知最大容量的情况下，建议 make 初始化，可以避免重新分配内存提升效率
            maxCap := 3
            intSlice2 := make([]int, 0, maxCap)
            fmt.Println(intSlice2)

            m2 := make(map[int]int) // 如果是 map 要先 make 才可以，否则 panic
            m2[1] = 1 // ok
            fmt.Println(m2)

            // 直接声明然后赋值就会 panic。有一些 struct 包含了 map 结构体成员，构造函数里需要注意初始化 map，否则直接赋值panic
            // https://stackoverflow.com/questions/27553399/golang-how-to-initialize-a-map-field-within-a-struct
            var m1 map[int]int
            m1[1] = 1          // NOTE: panic ! 注意这样会panic 啊！！!
            fmt.Println(m1)

            type T struct {
                m map[int][int]
            }
            func NewT() T {
                return T{m: make(map[int]int)}
                // return T{m: map[int]int{}}
            }
    }

    func testNilMap() {
        var nilm map[string]int
        nilm = nil
        // 直接对 nil map 取值和 获取长度不会 panic
        fmt.Println(nilm["a"], len(nilm))
        // 直接对 nil map 遍历也不会 panic
        for k, v := range nilm {
            fmt.Println(k, v)
        }
        // nilm["hehe"] = 1 // 但是不能赋值，会 panic。必须用 make 或者 empty map 初始化先
    }


go 没有内置的 set 结构
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
go 目前没有提供泛型，也没提供一个统一的 set 数据结构。可以使用 map[string]bool 来模拟 set(注意并发安全)。
或者使用第三方提供的 set 类型。

- https://github.com/deckarep/golang-set
- https://stackoverflow.com/questions/34018908/golang-why-dont-we-have-a-set-datastructure

编译 tag 的作用
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    // +build linux,386 darwin,!cgo

- https://golang.org/pkg/go/build/

Application auto build versioning
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

给 build 的二进制文件加上版本号，注意如果命令中输出有空格，需要加上单引号。
这样我们可以每次运行二进制文件的时候打印构建时间，当前的版本等信息。

.. code-block:: go

    // +build linux,386 darwin,!cgo
    package main

    import "fmt"
    var xyz string
    func main() {
        fmt.Println(xyz)
    }
    // $ go run -ldflags "-X main.xyz=abc" main.go
    // go build -ldflags "-X main.minversion=`date -u +.%Y%m%d.%H%M%S`" service.go
    // go build  -ldflags "-X 'main.time=$(date -u --rfc-3339=seconds)' -X 'main.git=$(git log --pretty=format:"%h" -1)'"  main.go

- https://stackoverflow.com/questions/11354518/application-auto-build-versioning


Go JSON 空值处理的一些坑，看示例
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    package main

    import (
            "encoding/json"
            "fmt"
    )

    // https://www.sohamkamani.com/blog/golang/2018-07-19-golang-omitempty/
    // omitempty 对于0值和，nil，pointer 的处理需要注意下坑。

    func testNormal() {
            type Dog struct {
                    Breed    string
                    WeightKg int
            }
            d := Dog{
                    Breed:    "dalmation",
                    WeightKg: 45,
            }
            b, _ := json.Marshal(d)
            fmt.Println(string(b)) // {"Breed":"dalmation","WeightKg":45}
    }

    func testOmit() {
            type Dog struct {
                    Breed    string
                    WeightKg int
            }
            d := Dog{
                    Breed: "pug",
            }
            b, _ := json.Marshal(d)
            fmt.Println(string(b)) //{"Breed":"pug","WeightKg":0}
            // 注意没填的字段输出0，如果不想输出0呢？比如想输出 null 或者压根不输出这个字段
    }

    func testOmitEmpty() {
            type Dog struct {
                    Breed string
                    // The first comma below is to separate the name tag from the omitempty tag
                    WeightKg int `json:",omitempty"`
            }
            d := Dog{
                    Breed: "pug",
            }
            b, _ := json.Marshal(d)
            fmt.Println(string(b)) // {"Breed":"pug"}
    }

    func testValuesCannotBeOmitted() {
            type dimension struct {
                    Height int
                    Width  int
            }

            type Dog struct {
                    Breed    string
                    WeightKg int
                    Size     dimension `json:",omitempty"`
            }

            d := Dog{
                    Breed: "pug",
            }
            b, _ := json.Marshal(d)
            fmt.Println(string(b)) //{"Breed":"pug","WeightKg":0,"Size":{"Height":0,"Width":0}}

    }

    func testValuesCannotBeOmittedButUsePointer() {
            type dimension struct {
                    Height int
                    Width  int
            }

            type Dog struct {
                    Breed    string
                    WeightKg int
                    Size     *dimension `json:",omitempty"` //和上一个不同在于这里使用指针
            }

            d := Dog{
                    Breed: "pug",
            }
            b, _ := json.Marshal(d)
            fmt.Println(string(b)) // {"Breed":"pug","WeightKg":0}

    }

    // The difference between 0, "" and nil
    // One issue which particularly caused me a lot a trouble is the case where you want to differentiate between a default value, and a zero value.
    //
    // For example, if we have a struct describing a resteraunt, with the number of seated customers as an attribute:
    func testZeroWillOmit() {
            type Restaurant struct {
                    NumberOfCustomers int `json:",omitempty"`
            }

            d := Restaurant{
                    NumberOfCustomers: 0,
            }
            b, _ := json.Marshal(d)
            fmt.Println(string(b)) // {}
            // 输出 {}， 0被省略了
    }

    func testZeroPointer() {
            type Restaurant struct {
                    NumberOfCustomers *int `json:",omitempty"`
            }
            d1 := Restaurant{}
            b, _ := json.Marshal(d1)
            fmt.Println(string(b)) //Prints: {}

            n := 0
            d2 := Restaurant{
                    NumberOfCustomers: &n,
            }
            b, _ = json.Marshal(d2)
            fmt.Println(string(b)) //Prints: {"NumberOfCustomers":0} ，总结一下就是值为 0 的 pointer 也不会省略字段
    }

    func main() {
            // testOmit()
            // testOmitEmpty()
            // testValuesCannotBeOmitted()
            // testValuesCannotBeOmittedButUsePointer()
            testZeroWillOmit()

    }

- https://www.sohamkamani.com/blog/golang/2018-07-19-golang-omitempty/
- https://ethancai.github.io/2016/06/23/bad-parts-about-json-serialization-in-Golang/

Go int/int64/float 和 string 转换示例
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    // 推荐一个更加强大的转换库：https://github.com/spf13/cast
    package main

    import (
            "fmt"
            "strconv"
    )
    /*
    或者安装 https://github.com/chubin/cheat.sh
    curl https://cht.sh/:cht.sh | sudo tee /usr/local/bin/cht.sh
    chmod +x /usr/local/bin/cht.sh

    之后输入以下语句查询答案：
    cht.sh go cht.sh go string to float64
    */

    func main() { // 测试 int 和 string(decimal) 互相转换的函数
            // https://yourbasic.org/golang/convert-int-to-string/
            // int -> string
            sint := strconv.Itoa(97)
            fmt.Println(sint, sint == "97")

            // byte -> string
            bytea := byte(1)
            bint := strconv.Itoa(int(bytea))
            fmt.Println(bint)

            // int64 -> string
            sint64 := strconv.FormatInt(int64(97), 10)
            fmt.Println(sint64, sint64 == "97")

            // int64 -> string (hex) ，十六进制
            sint64hex := strconv.FormatInt(int64(97), 16)
            fmt.Println(sint64hex, sint64hex == "61")

            // string -> int
            _int, _ := strconv.Atoi("97")
            fmt.Println(_int, _int == int(97))

            // string -> int64
            _int64, _ := strconv.ParseInt("97", 10, 64)
            fmt.Println(_int64, _int64 == int64(97))

            // https://stackoverflow.com/questions/30299649/parse-string-to-specific-type-of-int-int8-int16-int32-int64
            // string -> int32，注意 parseInt 始终返回的是 int64，所以还是需要 int32(n) 强转一下
            _int32, _ := strconv.ParseInt("97", 10, 32)
            fmt.Println(_int32, int32(_int32) == int32(97))

            // int32 -> string, https://stackoverflow.com/questions/39442167/convert-int32-to-string-in-golang
            strconv.FormatInt(int64(i), 10) // fast
            strconv.Itoa(int(i)) // fast
            fmt.Sprint(i) // slow

            // int -> int64 ，不会丢失精度
            var n int = 97
            fmt.Println(int64(n) == int64(97))

            // string -> float32/float64  https://yourbasic.org/golang/convert-string-to-float/
            f := "3.14159265"
            if s, err := strconv.ParseFloat(f, 32); err == nil {
                fmt.Println(s) // 3.1415927410125732
            }
            if s, err := strconv.ParseFloat(f, 64); err == nil {
                fmt.Println(s) // 3.14159265
            }

            // float -> string https://yourbasic.org/golang/convert-string-to-float/
            s := fmt.Sprintf("%f", 123.456)
    }

Go struct 如何设置默认值
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
Go 的结构体成员没法直接设置默认值，使用的是每个类型的默认值，可以 New 构造函数里设置。

.. code-block:: go

    // https://stackoverflow.com/questions/37135193/how-to-set-default-values-in-go-structs
    //Something is the structure we work with
    type Something struct {
         Text string
         DefaultText string
    }
    // NewSomething create new instance of Something
    func NewSomething(text string) Something {
       something := Something{}
       something.Text = text
       something.DefaultText = "default text"
       return something
    }

Go 如何使用枚举值 Enum
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
Go没有提供内置的枚举类型，不过可以使用自定义类型和常量值来实现枚举类型。
并且还可以给自定义的类型定义方法。

.. code-block:: go

    type Base int

    const (
            A Base = iota
            C
            T
            G
    )

- https://stackoverflow.com/questions/14426366/what-is-an-idiomatic-way-of-representing-enums-in-go
- https://blog.learngoprogramming.com/golang-const-type-enums-iota-bc4befd096d3


Go 如何断判非空字符串/slice
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

标准库实际上 `len(s) != 0` 和 `s != ""` 都有使用，我个人倾向于 `s != ""` 看起来更清楚，区分其他容器类型判断的方式。
比如如果使用 slice 可以使用 len(slice) == 0 判断是否为空。

Go 清空 slice 两种方式区别
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    // https://yourbasic.org/golang/clear-slice/
    func testClearSlice() {
        // 1. 赋值 nil 清空
        a := []string{"A", "B", "C"}
        a = nil                        // 这种方式会导致 gc 释放底层数组（假设没有其他引用)
        fmt.Println(a, len(a), cap(a)) // [] 0 0

        // 2. 使用 s = s[:0] , 注意底层 cap 还是 5
        b := []string{"A", "B", "C", "D", "E"}
        b = b[:0]                      // 不会清空底层数组
        fmt.Println(b, len(b), cap(b)) // [] 0 5
    }

Go 如何格式化参数
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

- https://yourbasic.org/golang/fmt-printf-reference-cheat-sheet/


命名返回值
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

go 的返回值可以命名，使用命名返回值有几个用处：

- 可以当成文档，直观的展示返回值的含义
- 自动初始化为类型的零值
- 返回的时候不用写很多参数名，直接用 return 就行
- 如果想要在 defer 中修改返回值，只能使用命名参数。例子如下
- 缺点：函数里很容易误用声明一个同名的参数就会被被覆盖了(shadow)
- 函数返回相同类型的两个或三个参数，或者如果从上下文中不清楚结果的含义，使用命名返回，其它情况不建议使用命名返回。

.. code-block:: go

    func namedReturn(i int) (ret int) {
        ret = i
        defer func() { ret++ }()

        return
    }

    func anonReturn(i int) int {
        ret := i
        defer func() { ret++ }() // 修改 ret 无效
        return ret
    }

    func main() {
        fmt.Println(namedReturn(0)) // 1
        fmt.Println(anonReturn(0))  // 0
    }


Go 如何复制map
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
注意 go 和其他很多编程语言一样，对于复合结构是浅拷贝，共享底层数据结构。几个变量指向同一个复合结构的时候注意修改一个对其他变量也是可见的。

.. code-block:: go

    // https://stackoverflow.com/questions/23057785/how-to-copy-a-map
    func copyMap(src map[string]string) map[string]string {
      res := make(map[string]string)
      for k, v := range src {
        res[k] = v
      }
      return res
    }

    func testShareMap() {
      am := []map[string]string{
        map[string]string{"a1": "a1", "b1": "b1"},
        map[string]string{"a2": "a2", "b2": "b2"},
      }
      bm := am
      bm[0]["a1"] = "testbm" // NOTE 这里修改了b，a 里边的也会变。共享 map
      fmt.Println(am)

      var cm []map[string]string
      for _, m := range am {
        cm = append(cm, copyMap(m))
      }
      cm[0]["a1"] = "testcm" // will not modify am
      fmt.Println(am)
    }

    func main() {
      testShareMap()
    }

循环变量与闭包问题 ⚠️
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
go 的循环变量是 per-loop 的而不是 per-iteration 绑定的，这个特性导致了很多隐蔽反直觉并且难以排查的bug。
go 官方目前仍在讨论是否要改善这个问题：https://github.com/golang/go/discussions/56010 。

举三个常见例子防止踩坑：

.. code-block:: go

  package main

  import (
      "fmt"
      "time"
  )

  // 示例1：goroutine 直接使用闭包变量
  func testForLoopGoroutine() {
      data := []string{"one", "two", "three"}
      for _, v := range data {
          go func() {
              fmt.Println(v) // 最终打印的都是一样的值，和期望不符
          }()
      }
      // output: three three three
  }

  // 两种方式解决：1.使用一个临时变量
  func testForLoopGoroutine1() {
      data := []string{"one", "two", "three"}
      for _, v := range data {
          v := v // 使用一个临时变量 v
          go func() {
              fmt.Println(v)
          }()
      }
      // one two three (may wrong order)
  }

  // 方法2：传给匿名goroutine参数
  func testForLoopGoroutine2() {
      data := []string{"one", "two", "three"}
      for _, v := range data {
          go func(in string) {
              fmt.Println(in)
          }(v)
      }
      // one two three (may wrong order)
  }

  // 示例2：对循环变量取地址
  type MyStruct struct{ i int }

  func testForLoopPointer() {
      values := []MyStruct{MyStruct{1}, MyStruct{2}, MyStruct{3}}
      output := []*MyStruct{}

      for _, v := range values {
          // v := v // 加上这一行才能打印1，2，3
          output = append(output, &v) // 如果不用临时变量，始终取到的是同一个地址
      }

      for _, o := range output {
          fmt.Println(o.i) // 都打印3
      }
  }

  // 示例3：接收者指针
  type field struct{ name string }

  func (p *field) print() { // 接收者是指针
      fmt.Println(p.name)
  }

  func testForLoopPointerReceiver() {
      data := []field{{"one"}, {"two"}, {"three"}} // 改成 []*field 可以修复
      for _, v := range data {
          // v := v       // NOTE：直接这样就可以解决。或者使用 struct 指针，[]*field 初始化
          go v.print() // print three three three
      }
  }

总结一下会出bug的常见情况：

- 在 goroutine 中直接使用循环变量(示例1)
- 循环中直接对循环变量取地址(示例2)
- 循环变量(非指针)调用函数是一个指针接收者，调用却直接传入循环变量值(示例3)

解决方式：

1. 创建一个临时局部变量(`v:=v`)
2. 作为参数传入(解决goroutine场景)

参考:

- https://nullprogram.com/blog/2014/06/06/ Per Loop vs. Per Iteration Bindings

Failed Type Assertions
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    package main

    import "fmt"

    func main() {
            var data interface{} = "hehe"
            // NOTE: 这里不要用 同名的 data 变量，比如换成 dataInt
            if data, ok := data.(int); ok {
                    fmt.Println("[is an int] value =>", data)
            } else {
                    fmt.Println("[not an int] value =>", data)
                    // NOTE ：注意 data 已经被失败的 type assert 赋值成了0
            }
    }

An interface holding a nil value is not nil
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

An interface holding nil value is not nil. An interface equals nil only if both type and value are nil.

.. code-block:: go

    package main
    import "fmt"
    func main() {
        var a interface{}
        fmt.Printf("a == nil is %t\n", a == nil) // a == nil is true
        var b interface{}
        var p *int = nil
        b = p
        fmt.Printf("b == nil is %t\n", b == nil) // b == nil is false
    }


逃逸分析
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
想要知道变量在 stask 还是 heap 分配使用 `go run -gcflags -m app.go`

报错：go test flag: flag provided but not defined
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

- https://stackoverflow.com/questions/29699982/go-test-flag-flag-provided-but-not-defined

Go Context 的坑
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
调用接收context的函数时要小心，要清楚context在什么时候cancel，什么行为会触发cancel。
笔者最近遇到一个问题是，在框架的 handler 函数返回之前，单独开一个 goroutine 创建一条 mysql 流水，但是handler 函数调用完
成之后框架会cancel，导致这个 mysql 传进去了框架函数带过来的 ctx cancel 之后执行失败，解决方式就是传一个单独的 context

.. code-block:: go

    func (h *Handler) handleFunc(ctx context.Context, req *Request, resp *Response) error {
        // ... 其他业务逻辑
        go func() { // 异步记录流水
            // 注意，这里不能直接用框架的 ctx，而是需要一个不被 cancel 的 context，否则执行会失败，改成 context.TODO()
            if err := postDao.CreatePostCreateRecord(context.TODO(), row); err != nil { // 正确!
            // if err := postDao.CreatePostCreateRecord(ctx, row); err != nil { // 错误！ ctx 很快被 cancel 导致流程失败
                log.Errorf("CreatePost postDao.CreatePostCreateRecord err:%+v", err)
            }
        }()
        return nil
    }

- https://zhuanlan.zhihu.com/p/34417106 《Go Context的踩坑经历》

Go rand 的坑
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
如果忘记调用 `rand.Seed(time.Now().UnixNano())` 每次重新运行返回结果是相同的，应该在程序初始化的时候比如 init 函数调用一次 seed。


redio tricks
--------------------------------------------------

redis 连接超时
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
默认是没有超时时间的，注意设置超时时间（connect/read/write)。所有的网络 client 都应该设置超时时间(参考P99等值)

- https://ms2008.github.io/2019/07/04/golang-redis-deadlock/

redis 单测如何 mock
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
reids mock 可以用 miniredis，以下是一个示例代码

.. code-block:: go

    package main

    import (
      "fmt"
      "os"
      "testing"

      "github.com/alicebob/miniredis"
      "github.com/go-redis/redis"
      "github.com/stretchr/testify/assert"
    )

    var followImpl *Follow

    func setupFollow() {
      fmt.Println("setup")
      mr, err := miniredis.Run()
      if err != nil {
        panic("init miniredis failed")
      }
      client := redis.NewClient(&redis.Options{
        Addr: mr.Addr(),
      })
      _ = client.Set("key", "wang", 0).Err()
      followImpl = &Follow{client: client}
    }

    func TestGet(t *testing.T) {
      val, err := followImpl.Get("key")
      followImpl.client.Set("key", "2", 0)
      fmt.Println(val, err)
      assert.Equal(t, val, "wang")
    }

    func TestPING(t *testing.T) {
      PING()
    }

    func TestMain(m *testing.M) {
      setupFollow()
      code := m.Run()
      os.Exit(code)
    }


网络相关
--------------------------------------------------

获取本机 ip
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    package main

    import (
        "fmt"
        "log"
        "net"
        "time"
    )

    var localIp string // 用一个全局变量或者缓存，防止高并发的时候重复频繁系统调用

    // GetIPAddr 获取 server IP
    func GetIPAddr() string {
        if localIp != "" {
            // fmt.Printf("use local ip %s\n", localIp)
            return localIp
        }
        addrs, err := net.InterfaceAddrs()
        if err != nil {
            return ""
        }

        for _, addr := range addrs {
            if ipnet, ok := addr.(*net.IPNet); ok && !ipnet.IP.IsLoopback() {
                if ipnet.IP.To4() != nil {
                    localIp = ipnet.IP.String()
                    return localIp
                }
            }
        }
        return ""
    }

    func main() {
        fmt.Println(GetIPAddr())
    }


Go 网络请求设置 Host 不起作用
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    req, err := http.NewRequest("GET", "http://bbb.com/", nil)
    if err != nil {
        log.Fatal(err)
    }
    req.Host = "aaa.com"
    // 注意以下不起作用，用 python 习惯使用 header 设置头了，但是 go 里边只能通过 req.Host 设置 host
    // req.Header.Set("Host", "www.example.org")  // 不起作用！！！ https://github.com/golang/go/issues/29865


Go panic 场景 ⚠️
--------------------------------------------------
在《Go 编程之旅》中总结了一些 panic 场景，写 go 的时候注意下，防止进程退出：

- 数组/切片越界。确保下标在数组长度范围内
- 空指针调用。比如访问一个 nil 结构体指针的成员, `a.b.c` 但是 b 是一个空指针就会 panic
- 过早关闭 HTTP 响应体
- 除以 0
- 向已经关闭的 channel 发送消息(或者多次close同一个 channel)
- 重复关闭 channel
- 关闭未初始化的 channel
- 跨协程的 panic 处理。main 协程中无法处理对子协程的 panic
- sync 计数为负数。
- 类型断言不匹配。`var a interface{} = 1; fmt.Println(a.(string))` 会 panic，建议用 `s,ok := a.(string)`
- 危险的 map

  - 赋值之前必须用 make 创建。注意访问 map 不存在的 key 不会 panic，而是返回 map 类型对应的零值，但是不能直接声明就赋值，而是要用make创建
  - 不要并发写原生 map。 需要加锁、使用 sync.Map 或者第三方并发安全的 map 比如 patrickmn/go-cache。当你声明一个全局map时，确认它是只读的

参考：

- https://xiaomi-info.github.io/2020/01/20/go-trample-panic-recover/
- https://zhuanlan.zhihu.com/p/534419732 Golang channel 三大坑，你踩过了嘛？


Go 内存泄露场景
--------------------------------------------------

- 切片引用的底层数组，一直没有释放(由于 string 切片时也会共用底层数组，所以使用不当也会造成内存泄漏)。

  - 尽量保证切片只作为局部变量使用，不会被传递到方法外，这样局部变量使用完后就会被回收
  - 如果不能保证切片作为局部变量且不会被传递，应该对切片进行拷贝。可以用 append 方法或者使用 copy 函数

- time.Ticker 忘记 stop。注意Ticker 和 Timer 是不同的。Timer 只会定时一次，而 Ticker 如果不 Stop，就会一直发送定时。建议用defer 进行stop
- channel 误用造成的泄露

  - 如果接收者需要在 channel 关闭之前提前退出，为防止内存泄漏，在发送者与接收者发送次数是一对一时，应设置 channel 缓冲队列为 1；
  - 在发送者与接收者的发送次数是多对多时，应使用专门的 stop channel 通知发送者关闭相应 channel。

参考：https://zhuanlan.zhihu.com/p/550956060


Go Web
--------------------------------------------------

滚动日志
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    package main

    import (
        "fmt"
        "os"
        "strings"

        "go.uber.org/zap"
        "go.uber.org/zap/zapcore"
        lumberjack "gopkg.in/natefinch/lumberjack.v2"
    )

    func NewLogger(filePath string, level zapcore.Level, maxSize int, maxBackups int, maxAge int, compress bool, serviceName string) *zap.Logger {
        core := newCore(filePath, level, maxSize, maxBackups, maxAge, compress)
        return zap.New(core, zap.AddCaller(), zap.Development(), zap.Fields(zap.String("serviceName", serviceName)))
    }

    func newCore(filePath string, level zapcore.Level, maxSize int, maxBackups int, maxAge int, compress bool) zapcore.Core {
        //日志文件路径配置
        hook := lumberjack.Logger{
            Filename:   filePath,   // 日志文件路径
            MaxSize:    maxSize,    // 每个日志文件保存的最大尺寸 单位：M
            MaxBackups: maxBackups, // 日志文件最多保存多少个备份
            MaxAge:     maxAge,     // 文件最多保存多少天
            Compress:   compress,   // 是否压缩
        }
        // 设置日志级别
        atomicLevel := zap.NewAtomicLevel()
        atomicLevel.SetLevel(level)
        //公用编码器
        encoderConfig := zapcore.EncoderConfig{
            TimeKey:        "time",
            LevelKey:       "level",
            NameKey:        "logger",
            CallerKey:      "linenum",
            MessageKey:     "msg",
            StacktraceKey:  "stacktrace",
            LineEnding:     zapcore.DefaultLineEnding,
            EncodeLevel:    zapcore.LowercaseLevelEncoder,  // 小写编码器
            EncodeTime:     zapcore.ISO8601TimeEncoder,     // ISO8601 UTC 时间格式
            EncodeDuration: zapcore.SecondsDurationEncoder, //
            EncodeCaller:   zapcore.FullCallerEncoder,      // 全路径编码器
            EncodeName:     zapcore.FullNameEncoder,
        }
        return zapcore.NewCore(
            zapcore.NewJSONEncoder(encoderConfig),                                           // json 格式编码器配置
            zapcore.NewMultiWriteSyncer(zapcore.AddSync(os.Stdout), zapcore.AddSync(&hook)), // 打印到控制台和文件
            atomicLevel, // 日志级别
        )
    }

    // 全局 logger 定义
    var Logger *zap.Logger

    func getZapLevel(level string) zapcore.Level {
        level = strings.ToLower(level)
        switch level {
        case "debug":
            return zapcore.DebugLevel
        case "info":
            return zapcore.InfoLevel
        case "warn":
            return zapcore.WarnLevel
        case "error":
            return zapcore.ErrorLevel
        case "dpanic":
            return zapcore.DPanicLevel
        case "fatal":
            return zapcore.FatalLevel
        default:
            panic(fmt.Errorf("invalid log level"))
        }
    }

    func init() {
        loglevel := "debug"
        Logger = NewLogger("./logs/service.log", getZapLevel(loglevel), 128, 30, 7, true, "UploadServer")
    }

    func main() {
        Logger.Info("hello go!")
    }
