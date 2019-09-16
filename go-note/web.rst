.. _goweb:

Go入门
=====================================================================

Go语言
--------------------------------------------------

- `A Tour of Go <https://tour.golang.org/welcome/1>`_
- `The-way-to-go <https://github.com/Unknwon/the-way-to-go_ZH_CN>`_
- `golang-developer-roadmap <https://github.com/Alikhll/golang-developer-roadmap>`_
- `How to Write Go Code <https://golang.org/doc/code.html>`_
- `Learning-golang <https://github.com/developer-learning/learning-golang>`_
- `Build Web Application With Golang <https://github.com/astaxie/build-web-application-with-golang>`_
- `Go 高级编程 <https://chai2010.cn/advanced-go-programming-book/>`_

Go Books
---------------------------------------------------------------
- https://github.com/dariubs/GoBooks

Go 开发工具
---------------------------------------------------------------
- Goland
- vim/Neovim + vim-go

Go idioms
--------------------------------------------------
- https://yourbasic.org/golang/switch-statement/


Go 错误处理
--------------------------------------------------
- https://github.com/juju/errors
- https://blog.golang.org/error-handling-and-go
- https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully


Go日志实践
--------------------------------------------------
- https://imhanjm.com/2017/05/19/go%20logger%20日志实践/

Go文档查询
--------------------------------------------------
- https://gowalker.org


GOPROXY 代理
--------------------------------------------------
如果有有一些库拉不下来又没有自己的代理，可以试试

export GOPROXY=https://goproxy.io


Web/RPC框架
--------------------------------------------------

- gin
- grpc

个人推荐使用 gin，当然你可以参考一下 star 选择别的框架

- https://github.com/gin-gonic/contrib gin各种组件
- https://github.com/mingrammer/go-web-framework-stars

Gin example
--------------------------------------------------
- https://github.com/vsouza/go-gin-boilerplate
- https://github.com/gothinkster/golang-gin-realworld-example-app

微服务
--------------------------------------------------
微服务框架：

- go kit: https://github.com/go-kit/kit
- kratos: https://github.com/bilibili/kratos B站go微服务框架

微服务代码示例：

- https://dzone.com/users/3214037/eriklupander.html 介绍 go 微服务的一系列博客
- https://github.com/callistaenterprise/goblog go 微服务代码示例和博客，介绍了微服务各种基础组件

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
但是静态语言不行，一般难点在于如何去模拟外部依赖(比如数据库请求，redis 请求等)：

- 接口(go 推荐面向接口编程，否则你很难编写单测)
- mysql: 如何 mock 数据库请求。使用 sqlmock，或者编写 dao 层 interface，然后 mock 这个dao层接口
- http: 使用 httpmock 来模拟请求返回值
- redis: 这里我试了下 miniredis 比较好用，基于 go 实现，无需真实的 redis server

也有一种方式在单测环境加入真实的db 和redis（比如 docker），然后单测读取测试环境的数据库来操作。
这样的好处是可以不使用各种 mock 库，直接操作真实的 mysql，测试代码写起来也更方便。

以下是用到的一些单测相关的库：

- testing: 内置库
- github.com/stretchr/testify/assert: 用来做断言 assert 方便
- gomock(mockgen): 静态语言难以像动态语言直接属性替换，所以一般我们基于接口编写代码，然后可以生成接口 mock
- sqlmock: 如果依赖了数据库 mysql 等，可以使用 sqlmock 模拟数据库返回内容。（或者就在测试环境用真实的 mysql，测试完清理插入的测试数据)
- httpmock: 用来 mock 调 http 请求
- github.com/alicebob/miniredis 可以用来 mock redis，无需启动真实的 resdis server。试了下非常好用，也不用使用 mock 和真实的 redis 了。个人强烈推荐
- bouk/monkey: 通过替换函数指针的方式修改任意函数的实现，如果以上都无法满足需求，可以用这种比较 hack 的方式。可能需要禁止编译器内联优化 `go test -gcflask=-l ./...`

参考：

- https://medium.com/@rosaniline/unit-testing-gorm-with-go-sqlmock-in-go-93cbce1f6b5b


Go 断点调试器dlv
---------------------------------------------------------------

.. code-block:: shell

   # 搜索函数，打断点，如果有同名函数的时候比较有用
   funcs FuncName

   # 打断点断点
   b main.main

   # go get -u github.com/derekparker/delve/cmd/dlv
   dlv debug main.go

   # 加上命令行参数
   # https://github.com/go-delve/delve/issues/562
   dlv debug ./cmd/unit-assignment-cli/main.go -- server


- https://yq.aliyun.com/articles/57578

Go Debug 调试工具
---------------------------------------------------------------
- go-spew: 用来打印一些复杂结构方便调试 https://github.com/davecgh/go-spew


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
- https://dave.cheney.net/practical-go
- https://github.com/codeship/go-best-practices

- https://12factor.net/zh_cn/
- https://go-proverbs.github.io go谚语，类似 python 之禅

Go List import
---------------------------------------------------------------

.. code-block:: shell

   # https://pmcgrath.net/how-to-get-golang-package-import-list
   go list -f '{{range $imp := .Imports}}{{printf "%s\n" $imp}}{{end}}' | sort
   go list -f '{{range $dep := .Deps}}{{printf "%s\n" $dep}}{{end}}' | xargs go list -f '{{if not .Standard}}{{.ImportPath}}{{end}}'


Go 常用框架
---------------------------------------------------------------
- web: gin/grpc/beego
- 配置解析: viper
- rpc: grpc
- mysql orm: gorm, sqlx
- redis: go-redis, redigo
- 异步任务框架: machinery/gocelery
- 熔断：hytrix-go
- 限流: ulule/limiter
- 日志: logrus
- 调试：go-spew
- 图片处理：https://github.com/h2non/imaginary

使用：

- https://zhuanlan.zhihu.com/p/22803609 redigo demo
- https://github.com/smallnest/gen gorm struct 生成工具
- https://blog.biezhi.me/2018/10/load-config-with-viper.html viper 解析配置

Go 底层实现
---------------------------------------------------------------
- https://draveness.me/golang/concurrency/golang-context.html
- https://github.com/tiancaiamao/go-internals/tree/master/zh

Go Profiler
---------------------------------------------------------------
- pprof
- github.com/uber/go-troch: Flame graph profiler for Go programs，火焰图工具，配合压测看性能瓶颈

- https://cizixs.com/2017/09/11/profiling-golang-program/

Goroutines
---------------------------------------------------------------
- https://medium.com/@vigneshsk/how-to-write-high-performance-code-in-golang-using-go-routines-227edf979c3c
- https://udhos.github.io/golang-concurrency-tricks/

Go 内存泄露
---------------------------------------------------------------
- https://go101.org/article/memory-leaking.html
- https://colobu.com/2019/08/28/go-memory-leak-i-dont-think-so/
