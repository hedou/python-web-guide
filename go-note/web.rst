.. _goweb:

Go入门
=====================================================================

go语言
--------------------------------------------------

`A Tour of Go <https://tour.golang.org/welcome/1>`_
`The-way-to-go <https://github.com/Unknwon/the-way-to-go_ZH_CN>`_
`golang-developer-roadmap <https://github.com/Alikhll/golang-developer-roadmap>`_
`How to Write Go Code <https://golang.org/doc/code.html>`_
`Learning-golang <https://github.com/developer-learning/learning-golang>`_


Go文档查询
--------------------------------------------------
- https://gowalker.org


GOPROXY 代理
--------------------------------------------------
export GOPROXY=https://goproxy.io


Web/RPC框架
--------------------------------------------------

- gin
- grpc


微服务
--------------------------------------------------
- go kit: https://github.com/go-kit/kit
- kratos: https://github.com/bilibili/kratos B站go微服务框架

Go package
--------------------------------------------------
- https://awesome-go.com/
- https://go-search.org/search?q=redis

Go项目Layout
--------------------------------------------------
- https://github.com/golang-standards/project-layout
- https://zhengyinyong.com/go-project-layout-design.html


单元测试(unittest)
--------------------------------------------------

`GoMock框架使用指南 <https://www.jianshu.com/p/f4e773a1b11f>`_
`如何写出优雅的 golang 代码 <https://draveness.me/golang-101>`_

静态语言编写单测相比动态语言要难一些，动态语言中比如 python 可以很容易用 mock.patch 来做属性/方法替换。
但是静态语言不行，一般难点在于如何去 mock 以下内容：

- 接口(go 推荐面向接口编程，否则你很难编写单测)
- mysql: 如何 mock 数据库请求。使用 sqlmock，或者编写 dao 层 interface，然后 mock 这个dao层接口
- http: 使用 httpmock 来模拟请求返回值
- redis: 这里我试了下 miniredis 比较好用，基于 go 实现，无需真实的 redis server

也有一种方式在单测环境加入真实的db 和redis（比如 docker），然后单测读取测试环境的数据库来操作。

以下是用到的一些单测相关的库：

- testing: 内置库
- github.com/stretchr/testify/assert: 用来做断言 assert 方便
- gomock(mockgen): 静态语言难以像动态语言直接属性替换，所以一般我们基于接口编写代码，然后可以生成接口 mock
- sqlmock: 如果依赖了数据库 mysql 等，可以使用 sqlmock 模拟数据库返回内容
- httpmock: 用来 mock 调 http 请求
- github.com/alicebob/miniredis 可以用来 mock redis，无需启动真实的 resdis server
- bouk/monkey: 通过替换函数指针的方式修改任意函数的实现，如果以上都无法满足需求，可以用这种比较 hack 的方式。可能需要禁止编译器内联优化 `go test -gcflask=-l ./...`


Go 调试器dlv
---------------------------------------------------------------

.. code-block:: shell

   # 搜索函数，打断点
   funcs FuncName

   # 断点
   b main.main

   # go get -u github.com/derekparker/delve/cmd/dlv
   dlv debug main.go

   # 加上命令行参数
   # https://github.com/go-delve/delve/issues/562
   dlv debug ./cmd/unit-assignment-cli/main.go -- server


- https://yq.aliyun.com/articles/57578


Go Books
---------------------------------------------------------------
- https://github.com/dariubs/GoBooks


Go vs. Python
---------------------------------------------------------------
- http://govspy.peterbe.com/


Go Best practice
---------------------------------------------------------------
- https://draveness.me/golang-101 如何写出优雅的 golang 代码(好文推荐)
- https://github.com/golang/go/wiki/CodeReviewComments 作为 effective go 补充
- https://peter.bourgon.org/go-best-practices-2016/
- https://dave.cheney.net/practical-go/presentations/qcon-china.html
- https://golang.org/doc/effective_go.html
- https://talks.golang.org/2013/bestpractices.slide

- https://12factor.net/zh_cn/

Go List import
---------------------------------------------------------------

.. code-block:: shell

   # https://pmcgrath.net/how-to-get-golang-package-import-list
   go list -f '{{range $imp := .Imports}}{{printf "%s\n" $imp}}{{end}}' | sort
   go list -f '{{range $dep := .Deps}}{{printf "%s\n" $dep}}{{end}}' | xargs go list -f '{{if not .Standard}}{{.ImportPath}}{{end}}'

Go 技术雷达
---------------------------------------------------------------
- web: gin
- rpc: grpc
- mysql orm: gorm
- redis: go-redis/redigo


Go 底层实现
---------------------------------------------------------------
- https://draveness.me/golang/concurrency/golang-context.html
