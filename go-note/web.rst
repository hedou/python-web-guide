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


单元测试
--------------------------------------------------

`GoMock框架使用指南 <https://www.jianshu.com/p/f4e773a1b11f>`_


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
- redis: go-redis
