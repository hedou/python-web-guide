.. _gotricks:

Go踩坑
=====================================================================

go初学者常见错误
--------------------------------------------------

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

编译 tag 的作用
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: go

    // +build linux,386 darwin,!cgo

- https://golang.org/pkg/go/build/


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

redio tricks
--------------------------------------------------
注意设置超时时间。

- https://ms2008.github.io/2019/07/04/golang-redis-deadlock/
