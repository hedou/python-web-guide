.. _gotricks:

Go踩坑
=====================================================================

go初学者常见错误
---------------------------------------------------------------
强烈建议 go 新手过一遍下边这篇文章，go 写多了你一定会遇到很多里边总结的坑。

- `50 Shades of Go: Traps, Gotchas, and Common Mistakes for New Golang Devs  <http://devs.cloudimmunity.com/gotchas-and-common-mistakes-in-go-golang/>`_

Go tricks
--------------------------------------------------

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

Go 运行单个测试文件报错 undefined？
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

执行命令时加入这个测试文件需要引用的源码文件，在命令行后方的文件都会被加载到command-line-arguments中进行编译

- https://www.cnblogs.com/Detector/p/10010292.html
- https://stackoverflow.com/questions/14723229/go-test-cant-find-function-in-a-same-package

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

go 没有内置的 set 结构
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
go 目前没有提供泛型，也没提供一个统一的 set 数据结构。可以使用 map[string]bool 来模拟 set。
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

Go int/int64 和 string 转换示例
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    package main

    import (
            "fmt"
            "strconv"
    )

    func main() { // 测试 int 和 string(decimal) 互相转换的函数
            // https://yourbasic.org/golang/convert-int-to-string/
            // int -> string
            sint := strconv.Itoa(97)
            fmt.Println(sint, sint == "97")

            // int64 -> string
            sint64 := strconv.FormatInt(int64(97), 10)
            fmt.Println(sint64, sint64 == "97")

            // int64 -> string (hex)
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

            // int -> int64 ，不会丢失精度
            var n int = 97
            fmt.Println(int64(n) == int64(97))
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


Go 如何断判非空字符串
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

标准库实际上 `len(s) != 0` 和 `s != ""` 都有使用，我个人倾向于 `s != ""` 看起来更清楚，区分其他容器类型判断的方式。

Go 如何格式化参数
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

- https://yourbasic.org/golang/fmt-printf-reference-cheat-sheet/

Go 如何复制map
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
注意 go 和其他很多编程语言一样，对于复合结构是浅拷贝。几个变量指向同一个复合结构的时候注意修改一个对其他变量也是可见的。

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


redio tricks
--------------------------------------------------

redis 连接超时
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
默认是没有超时时间的，注意设置超时时间（connect/read/write)。

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
