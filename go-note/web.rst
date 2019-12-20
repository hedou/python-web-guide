.. _goweb:

Go入门
=====================================================================

Go语言入门和深入
--------------------------------------------------

- `A Tour of Go <https://tour.golang.org/welcome/1>`_
- `Go by Example <https://gobyexample.com>`_
- `Learn Go with Tests <https://quii.gitbook.io/learn-go-with-tests/>`_
- `The-way-to-go <https://github.com/Unknwon/the-way-to-go_ZH_CN>`_
- `golang-developer-roadmap <https://github.com/Alikhll/golang-developer-roadmap>`_
- `How to Write Go Code <https://golang.org/doc/code.html>`_
- `Learning-golang <https://github.com/developer-learning/learning-golang>`_
- `Build Web Application With Golang <https://github.com/astaxie/build-web-application-with-golang>`_
- `Go 高级编程 <https://chai2010.cn/advanced-go-programming-book/>`_
- `Go 101 <https://go101.org/article/101.html>`_ 包含了很多基础和高级主题

Go Books
---------------------------------------------------------------
- https://github.com/dariubs/GoBooks

Go 开发工具
---------------------------------------------------------------
- Goland
- vim/Neovim + vim-go
- https://github.com/fedir/go-tooling-cheat-sheet/blob/master/go-tooling-cheat-sheet.pdf

博客:

- https://ictar.xyz/2019/05/14/an-overview-of-go-tooling/


Go idioms
--------------------------------------------------
- https://yourbasic.org/golang/switch-statement/


Go 错误处理
--------------------------------------------------
- https://github.com/juju/errors
- https://blog.golang.org/error-handling-and-go
- https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully
- https://zhuanlan.zhihu.com/p/82985617 Golang error 的突围


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
- https://github.com/e421083458/gin_scaffold gin 脚手架
- https://github.com/mingrammer/go-web-framework-stars

Gin example
--------------------------------------------------
- https://github.com/EDDYCJY/go-gin-example
- https://github.com/vsouza/go-gin-boilerplate
- https://github.com/gothinkster/golang-gin-realworld-example-app

gin 实战博客:

- https://segmentfault.com/a/1190000013808421  gin 连载博客
- https://www.cnblogs.com/xinliangcoder/p/11212573.html logrus日志
- https://marcoma.xyz/2019/03/17/gin-tutorial-7/

微服务
--------------------------------------------------
微服务框架：

- go kit: https://github.com/go-kit/kit
- kratos: https://github.com/bilibili/kratos B站go微服务框架

微服务代码示例：

- https://dzone.com/users/3214037/eriklupander.html 介绍 go 微服务的一系列博客
- https://github.com/callistaenterprise/goblog go 微服务代码示例和博客，介绍了微服务各种基础组件
- https://github.com/yun-mu/MicroServicePractice 微服务实践

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
但是静态语言不行，一般难点在于如何去模拟外部依赖(比如数据库/rpc请求，redis 请求等)：

- 接口(go 推荐面向接口编程，否则你很难使用 gomock 来编写单测)
- mysql: 如何 mock 数据库请求。使用 sqlmock，或者编写 dao 层 interface，然后 mock 这个dao层接口
- http: 使用 httpmock 来模拟请求返回值
- redis: 这里我试了下 miniredis 比较好用，基于 go 实现，无需真实的 redis server

也有一种方式在单测环境加入真实的db 和redis（比如 docker），然后单测读取测试环境的数据库来操作。
这样的好处是可以不使用各种 mock 库，直接操作真实的 mysql，测试代码写起来也更方便。

以下是一些单测相关的库：

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
- dlv: 断点调试器

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
- https://github.com/uber-go/guide/blob/master/style.md   uber 的 go 规范

- https://12factor.net/zh_cn/
- https://go-proverbs.github.io go谚语，类似 python 之禅

Go List import
---------------------------------------------------------------

.. code-block:: shell

   # https://pmcgrath.net/how-to-get-golang-package-import-list
   go list -f '{{range $imp := .Imports}}{{printf "%s\n" $imp}}{{end}}' | sort
   go list -f '{{range $dep := .Deps}}{{printf "%s\n" $dep}}{{end}}' | xargs go list -f '{{if not .Standard}}{{.ImportPath}}{{end}}'


Go 常用框架(工具)
---------------------------------------------------------------
技术选型一般选择接口稳定，更新快，生态相对成熟，star 数量较高，用户广泛的库，坑少一点。
前后分离时代用 gin 之类的框架写app后台还是挺快的，但是感觉做并发不高的内部后台业务还是用脚本python/php之类的更快。
以下第三方库均可以通过 google + 关键词搜索到，同一行尽量按照流行程度从前往后列举，默认都是 github 上的包。

- web/rpc框架: gin, grpc, beego
- 参数验证：go-playground/validator, bytedance/go-tagexpr
- json处理转换：go-simplejson/mapstructure
- 配置解析: viper(兼容很多格式)
- mysql orm: gorm, xorm, sqlx
- redis: go-redis, redigo
- Kafka: Shopify/sarama
- Elasticsearch: olivere/elastic, elastic/elasticsearch
- mongodb: mongodb/mongo-go-driver
- id生成器: rx/xid
- cache(in memory): patrickmn/go-cache
- 并发/协程池(star 数排序)：

  - https://github.com/rafaeldias/async
  - https://github.com/Jeffail/tunny
  - https://github.com/benmanns/goworker
- 异步任务框架: machinery, gocelery
- 定时任务：robfig/cron, ouiqiang/gocron
- 熔断：hystrix-go, eapache/go-resiliency, cep21/circuit
- 限流: ulule/limiter, didip/tollbooth
- 日志: logrus, zap, mumberjack
- 调试：go-spew/dlv
- 图片处理：h2non/imaginary
- 网络编程：fatih/pool
- 表格：go-echarts
- 转换工具：

  - sql2go(sql -> go struct): http://stming.cn/tool/sql2go.html
  - curl2go(curl -> go http code): https://mholt.github.io/curl-to-go/
  - Json2go(json -> go struct): https://mholt.github.io/json-to-go/
- 热编译工具：gowatch
- 静态检查：golangci-lint
- 网络代理：goproxy
- 命令行: cobra
- 字符串处理工具：huandu/xstrings

工具:

- https://github.com/smallnest/gen gorm struct 生成工具
- https://mholt.github.io/json-to-go/ json 转 go struct
- https://protogen.marcgravell.com/decode proto decode 工具

博客：

- https://zhuanlan.zhihu.com/p/22803609 redigo demo
- https://blog.biezhi.me/2018/10/load-config-with-viper.html viper 解析配置

Go 底层实现(源码)
---------------------------------------------------------------
- https://draveness.me/golang/concurrency/golang-context.html
- https://github.com/tiancaiamao/go-internals/tree/master/zh
- https://zhuanlan.zhihu.com/p/80853548 深度解密Go语言之 scheduler
- https://github.com/cch123/golang-notes

Go Profiler
---------------------------------------------------------------
- pprof
- github.com/uber/go-troch: Flame graph profiler for Go programs，火焰图工具，配合压测看性能瓶颈
- https://cizixs.com/2017/09/11/profiling-golang-program/
- https://software.intel.com/en-us/blogs/2014/05/10/debugging-performance-issues-in-go-programs

Go 性能优化
---------------------------------------------------------------
- https://github.com/dgryski/go-perfbook
- https://dave.cheney.net/high-performance-go-workshop/dotgo-paris.html
- https://stephen.sh/posts/quick-go-performance-improvements

Goroutines
---------------------------------------------------------------
- https://medium.com/@vigneshsk/how-to-write-high-performance-code-in-golang-using-go-routines-227edf979c3c
- https://udhos.github.io/golang-concurrency-tricks/

Go 内存泄露
---------------------------------------------------------------
- https://go101.org/article/memory-leaking.html
- https://colobu.com/2019/08/28/go-memory-leak-i-dont-think-so/

Go源码阅读
---------------------------------------------------------------

.. image:: ../_image/goweb/gocode阅读.png
