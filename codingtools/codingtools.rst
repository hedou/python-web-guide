.. _codingtools:

开发和编程工具
=====================================================================

..

  Do one thing, and do it well. - A principle of Unix philosopy

工欲善其事，必先利其器(装逼工具)
--------------------------------------------------

- Pycharm。专业的python IDE，功能很强大，特别喜欢它的代码merge工具，不想被编辑器折腾死的推荐直接使用，五星级推荐。(除了内存占用大点)。如果你不喜欢折腾编辑器，请直接用 IDE，经常看见一些用裸编辑器写代码的，代码规范检测都没有。
- vim。本人比较喜欢的编辑器，平常写代码、博客、文档等使用频繁，配上各种插件编辑效率很高。http://vimawesome.com/ 可以到这个上面安装排名靠前的那些插件，能够大大提高编辑效率，部分替代IDE(本人装了六七十个插件，满足各种变态的编辑需求)。其他优秀的编辑器sublime，atom，vscode，emacs等根据个人喜好来吧，不过vim等终端友好的编辑器方便在服务器上直接写代码，和本地体验一样，缺点就是补全和跳转支持不完善，对新人不友好，也可以 Pycharm  和 vim插件配合。(在google搜索python awesome等可以在github上搜索到一些awesome项目，总结了该语言很多技术工具)。网上还有很多牛人开源了自己的 dotfiles，我们可以参考下别人的 vimrc 配置。
- neovim: 新时代的 vim，我在这个配置(https://github.com/PegasusWang/vim-config)上自定义了自己的配置，使用起来性能和反应速度上远超原生的老古董 vim，目前笔者已经全面迁移到 neovim，用着很爽。感兴趣可以关注笔者知乎专栏，我录了一些针对初学者的教学视频。
- ranger: 用 vim 的方式管理文件，替代 mac 下难用的 finder。
- oni: https://github.com/onivim/oni/ 构建在 neovim 上的 IDE。还有 VimR 等项目。
- meld/vimdiff/kdiff3: 跨平台的文件对比和冲突合并工具。
- tmux/tmuxp。比screen好用，可以用来分屏，托管进程等，服务器端必备神器，ubuntu下基本就不用使用terminator之类的分屏工具了。最近看youtube视频还发现有人在服务器上使用tmux和vim结对编程，两个人同时attach到一个session里，基情四射。
- wemux: tmux 共享，https://github.com/zolrath/wemux
- sshfs: 本地挂在服务器文件夹
- tmate: https://tmate.io 终端共享工具，结对编程。很多现代化编辑器 vscode, atom 提供结对编程的插件。
- asciinema: 终端会话记录工具。https://asciinema.org/
- oh-my-zsh。替代原生的bash shell，提供了好多方便的特性和漂亮主题，支持插件比如 zsh autocomplete。linux/mac下vim+tmux+zsh简直是绝配，甚至可以直接在服务器上方便地撸代码，跟本地开发体验没区别。
- item2(mac)。替代原生的终端。https://medium.com/@RyanDavidson/make-your-terminal-more-colourful-and-productive-with-iterm2-and-zsh-11b91607b98c
- brew(mac)。类似ubuntu下的apt-get，可以方便安转各种软件和工具。
- Alfred(mac): mac 下一款功能强大的工具，不过我一般只用它快速打开软件(比如直接输入app名前缀快速切换不同app)。可以用 python 编写一些自己的 workflow 提高效率(https://github.com/deanishe/alfred-workflow)，比如把时间戳转成日期等。 https://github.com/derimagia/awesome-alfred-workflows
- Dash(mac): 强悍的文档查询工具。支持非常多编程语言和框架。windows 和 linux 可以用 Zeal。或者 https://devdocs.io/
- devdocs.io: 文档查询工具
- Karabiner-Elements(mac): 改键工具 https://github.com/tekezo/Karabiner-Elements 可以用来禁用内置键盘
- autojump。方便在命令行里来回跳转目录。
- Magnet/chunkwm 窗口管理辅助工具，拖拽窗口到屏幕边缘可以自动半屏， 全屏或者四分之一屏幕，还可以设定快捷键完成分屏
- tldr: 更好的man手册。linux 命令很多，而且linux 的 man 手册比较难用，可以用这个 tldr 替代。https://github.com/tldr-pages/tldr
- EditorConfig: http://editorconfig.org/ 用来统一编辑器配置。如果成员用不同的操作系统和编辑器，建议使用。尤其是对于 python 这种使用缩进的语言
- mac-setup: https://github.com/sb2nov/mac-setup mac 下各种编程语言开发环境配置指引
- CheatSheet: https://www.mediaatelier.com/CheatSheet/ 显示 mac 快捷键

一定要有个趁手的开发工具(它甚至比你女朋友都重要)，不管是IDE还是编辑器，你程序员生涯的小半辈子都在和它打交道(提升编辑效率的秘诀在于多用键盘快捷键，少用鼠标，以及可以高度定制、和扩展功能的编辑器)。甚至编程字体你都要谨慎选取，比如字体可以很好区分'1', 'l', 'I', '0', 'O', 'S', '5'等易混淆字符，给浏览代码带来便利。如果使用的是mac可以google下 "Mac OS X development environment setup"，有惊喜呦。最后注意你用编辑器的话一定要用 pylint，pep8 检测插件，否则不遵守规范可能会导致用 IDE 打开项目后一堆警告(别人会想问候你祖宗的)。
更多 mac 工具可以参考：https://github.com/jaywcjlove/awesome-mac 。搜 awesome-python 或者 awesome-flask 等有很多类似项目。

一些提升效率的建议：

- 指法用对，练习盲打
- 熟悉你的开发工具，各种 IDE 或者编辑器快捷键。vim 等高度定制的编辑器允许你按照自己的习惯修改快捷键
- 学习一门脚本语言（编写自动化脚本）
- 多用键盘快捷键少用鼠标。修改系统键盘按键，比如 capslock 使用频率比较低，一般我改成了 ctrl 键，因为用开发工具频繁使用到 ctrl
- 自动化（比如监听文件变动刷新浏览器、重启http服务等）
- 用好终端和命令行工具（我建议你用 mac 或者 linux 系统，因为 server 大多跑的 linux，熟悉命令行会给你调试和面试带来便利）

* `《使用vim+tmux+zsh+autojump高效工作》 <http://ningning.today/2016/11/09/tools/vim-tmux-zsh-autojump/>`_
* `《玩转 vim 与 Terminal (视频)》 <https://zhuanlan.zhihu.com/vim-video>`_


Mac 终端快速配置
--------------------------------------
- 安装iterm2 替代难看的原生终端
- 安装 home brew 方便装各种工具
- 安装 oh-my-zsh or fish
- 安装 oh-my-zsh 主题 powerlevel9k 或者 spaceship。推荐使用异步的更快的 https://github.com/romkatv/powerlevel10k。 常见其他主题有(pure, powerlevel9k, spaceship)
- 安装 vim / spacevim /neovim
- 最好安装 tmux 用来替代iterm2 分屏
- 按需安装 htop/autojump/z/fzf/ag 等命令行工具(使用 brew 可以安装)

https://github.com/bhilburn/powerlevel9k/wiki/Show-Off-Your-Config

打字速度练习
--------------------------------------
- https://typing.io/
- https://www.keybr.com/
- http://www.speedcoder.net/


Git 相关
--------------------------------------
- gitx(mac):方便查看代码提交历史，便于了解整个代码仓库是怎样一步步构建的。http://gitx.frim.nl/user_manual.html
- tig: text-mode interface for git. 喜欢命令行的可以尝试下。 https://github.com/jonas/tig
- git-extras: 提供了很多方便的 git 工具，比如 git summary 可以输出代码的提交统计。https://github.com/tj/git-extras
- git-cz: https://github.com/commitizen/cz-cli 用来统一 git commmit 提交信息，代替 gitmessage
- pre-commit: git pre commit 工具。 https://pre-commit.com/


Chrome 开发者插件
--------------------------------------
- vimium: chrome 插件，可以用 vim 的方式操作浏览器，很方便，不用鼠标也能完成大部分操作。更强大替代品：SurfingKeys, Cvim
- FE助手：前端插件，Json 格式化等很多有用的工具
- Octotree: Chrome github 浏览插件，可以把 github 项目的目录树结构展示出来，非常方便
- Github Hovercard: 可以在浏览 github 的时候展示一些链接信息，比如可以查看作者的个人页
- Momentum: 美化 Chrome 界面的插件，有很多漂亮的图片
- Onetab: 用来整合 chrome tab到一个页面，如果打开了太多窗口特别占用资源的时候可以使用
- [LGTM](https://chrome.google.com/webstore/detail/lgtm/oeacdmeoegfagkmiecjjikpfgebmalof): 有些开发过程中，需要一个同事review 代码后评价 LGTM(Looks Good To Me)才能 merge 代码，这里是图片版的 LGTM


代码辅助和检测工具
--------------------------------------
- pylint: 代码静态检测工具，请务必集成在你的编辑器或者IDE里（推荐）。能帮你少犯很多错误，动态语言写项目要十分谨慎，非常容易犯错。或者在CI加上 hook 每次 push 代码的时候检测。pylintrc 参考：https://github.com/PegasusWang/linux_config/blob/master/pylintrc 这里我忽略了很多无关紧要的提示，默认的 pylint 配置对代码检查实在是太严格了，很多老鸟也过不了。我敢打赌大部分 python 项目用默认 pylint 检查都是不及格分。（pylint 会给代码算个分, 10分制）
- mypy: 类型检查工具，结合 python3 的 type hint 或者 python2 中的类型注释可以做类型检查。https://mypy.readthedocs.io/en/latest/cheat_sheet_py3.html
- pep8: python代码风格检测工具(推荐)。懒人可以试试 autopep8 工具，自动格式化。所有人的代码都过一遍 pylint 和 autopep8(放宽行长度) 看起来就比较一致了。甚至可以配置编辑器保存后自动执行 autopep8，类似 gofmt
- autopep8/yapf: python 代码自动格式化工具，懒人必备。都可以集成到 vim 里，比如使用  Plugin 'Chiel92/vim-autoformat'  工具一键格式化。不过注意有时会无法正确处理多重缩进，这个比较危险，代码逻辑都变了，还是自己写代码的时候注意下格式。
- prospector: 集成了众多python代码检测工具
- mccabe: 圈复杂度检测工具。McCabe 是一种度量程序复杂度的方法，如果单个子程序复杂度过高，或许就需要拆分逻辑提高程序的易读性。
- pyflakes
- bandit: 用于Python代码的安全性分析，openstack 的项目 https://github.com/openstack/bandit
- rope，可以用来重构等，功能强大。笔者经常用rope自动帮我重新整理导入的包顺序。
- python-mode: 一个vim插件，有很多 python 补全，语法检测等支持。并且集成了很多 python 工具(pylint,pep8等)，笔者正在用。
- jedi-vim: 一个 vim 插件，python 支持补全和重构。注意和 rope 的自动补全有冲突，不要同时启用。
- Pyreverse: 代码 UML 生成工具, 帮助我们理解继承关系 (https://pythonhosted.org/theape/documentation/developer/explorations/explore_graphs/explore_pyreverse.html)
- Epydoc: Automatic API Documentation Generation for Python
- 2to3/python-modernize: python2 转 python3 工具。目前 Instagram 已经全面迁移到 python3
- 编写2/3兼容代码：http://python-future.org/compatible_idioms.html

* `《[转] Instagram 在 PyCon 2017 的演讲摘要》 <https://zhuanlan.zhihu.com/p/27232791>`_

我觉得对于动态语言使用好静态代码检测工具还是很有必要的，最好集成在你的开发工具里(比如使用vim的python-mode插件可以很容易整合这几个代码检测工具)，辅助你写出高质量代码，否则大型动态语言项目维护起来就是灾难。python会给你一种代码很好写的错觉，不严格要求经常会写出来难以维护的烂代码，甚至导致代码仓库失控。通过 pep8、pylint、mccae 检测过的代码如果警告和错误都消除以后，从代码风格来说基本是没有大问题的，笔者一开始用的时候也是各种警告，修正过很多代码警告以后，以后代码就越来越规范和整洁了。https://github.com/PyCQA 。对于懒人的话直接用 autopep8 ，再也不用纠结格式问题了。目前笔者在公司的一些后端项目中就加入了 flake8 和 pylint 检测（自定义了 pylintrc 文件忽略一些无伤大雅的警告），代码写糙了 CI 都过不了。
我个人强烈建议，所有的人用 isort 整理包导入顺序，用 autopep8 格式化代码，用 pylint 静态检测，（笔者目前的小团队就是这么做的），这样提交的代码格式会非常一致，而且代码非常干净，大项目也不容易失控，动态语言写项目真的很容易出错。能用工具就尽量用工具帮我们解决格式等问题，多余的精力用来思考代码逻辑本身。

项目工具
--------------------------------------
- pigar: 找出项目使用到的依赖库
- buildout: 项目构建工具
- pyenv/virtualenv/pipenv：多版本管理
- cloc: 命令行代码行数统计工具


代码仓库托管
---------------------------------------
- gitlab: 公司用得多
- github: 著名的程序员同性交友网站
- bitbucket: 类似 github，好处是支持免费的私有仓库。当你不想共享代码的时候可以用


项目模板脚手架
--------------------------------------
微服务化的时代经常需要创建很多类似的项目代码模板，这个时候项目脚手架就分方便了。
统一的项目模板对于运维和开发都比较重要，有利于降低维护成本。

- cookiecutter: 一系列项目模板生成工具，懒人必备。https://github.com/audreyr/cookiecutter。笔者之前内部项目就直接用 flask-cookiecutter 直接生成的。
- yeoman: http://yeoman.io/generators/ 前端项目模板生成工具
- ant-design: 后端管理后台项目解决方案 https://ant.design/docs/react/practical-projects-cn


持续集成
--------------------------------------
- gitlab
- Travis CI
- Jenkins
- Sonar: https://www.sonarqube.org/ 代码质量管理

Api 工具
--------------------------------------
- checklist: http://python.apichecklist.com/

DSL
--------------------------------------
- PLY
- PyParsing: 用来实现 DSL 比较方便。
- Parsley


测试工具
--------------------------------------
- py.test
- nosetest
- unittest
- tox
- mock: mocking makes unit testing easier

文档写书工具
--------------------------------------
- google doc/石墨: 支持多人协作编辑
- gitbook + markdown: 可以写文档或电子书
- doocer: http://doocer.com/ 写 kindle 电子书工具
- sphinx + readthedoc(或者 mkdocs，支持 markdown) （代码即文档），python 项目很多在用这个生成文档。这本小书就是这么写出来的。`编写《Redis 设计与实现》时用到的工具 <http://blog.huangz.me/diary/2013/tools-for-writing-redisbook.html>`_
- swagger/apidocjs: 适合写 restful 文档。如果使用 grpc 可以直接生成。
- jupyter(ipython) notebook，可以做笔记或者代码演示或者ppt，支持rst，md等格式，搞数据科学的人用得比较多，配合 RISE (https://github.com/damianavila/RISE) 可以做代码交互式 slideshow，非常好的工具
- Confluence: 适合作为团队的项目文档工具，团队大了以后文档还是很重要的
- vimwiki/emacs org-mode: 依赖于vim/emacs 编辑器，可以做个人笔记，不过笔者还是比较倾向于独立于编辑器的工具
- Graphviz: 通过编写代码来生成图片 http://graphviz.org/
- pandoc: 用于各种格式文档之间的转换，比如 html->markdown, html->rst, markdown->rst

静态博客工具
--------------------------------------
静态建站工具允许我们用 github pages 建立静态博客，省去了服务器的费用。笔者的 https://pegasuswang.github.io 就是基于 hexo 搭建

- hexo: 基于 nodejs 编写的静态博客工具 https://hexo.io/zh-cn/

日志、异常收集工具
--------------------------------------

- Sentry: 用来记录异常非常好用，能看到完善的栈信息，方便排错。
- Fluentd
- ELK

管理及运维、监控工具(devops很火)
--------------------------------------
- Supervisor.进程管理
- Fabric.应用部署
- docker/k8s.最近比较火的容器技术。很多采用微服务架构的公司使用 docker 作为容器部署服务，或者构建一致的开发环境
- SaltStack和Ansible. 配置管理
- StatsD\Graphite等web监控

调试工具
--------------------------------------
- IPython/Bpython: 代替原生的解释器，支持补全，语法高亮等
- ipdb/pdb: ipdb 支持自动补全，比原生的 pdb 要好用一些。
- pdbpp: https://pypi.org/project/pdbpp/
- curl: 如果不想使用 postman 等 GUI 工具（比如在服务器上本地测试无法使用这种工具），可以用 curl 命令或者 python requests 库模拟请求
- https://curl.trillworks.com/ 把 curl 命令参数转成 requests 代码。 https://github.com/NickCarneiro/curlconverter/。
- httpie : 类似 curl 但是参数更加友好的命令行请求工具
- postman: 接口调试 gui 工具，也可以导出成各种编程语言的 HTTP 请求代码
- httpbin.org
- curl/requests 互相转化: https://github.com/oeegor/curlify https://github.com/spulec/uncurl

调试小技巧：使用 chrome 开发者工具右键请求点击copy as curl，然后可以用 uncurl 转成 requests请求调试代码。


抓包和下载工具
--------------------------------------
- mitmproxy: 用 python 实现的终端命令行抓包工具，可以将请求直接导出成 curl 请求，python 代码甚至 locust 测试脚本，非常方便，笔者经常用来抓包和调试。
- charles: 抓包软件(收费)


爬虫相关
--------------------------------------
- Scrapy: 知名的爬虫框架。生态比较丰富
- pyspider: 国人写的一个不错的爬虫框架
- requests: 一般小爬虫用 requests 绰绰有余。
- lxml/BeautifulSoup/pyquery: 解析 html，xml 等。
- tornado: 异步的 http client 可以写爬虫
- redis/celery: 实现队列、异步爬虫。异步方案也比较多
- phantomjs/puppeteer: 用来处理动态网站。puppeteer 基于 nodejs
- portia: 类似造数、八爪鱼之类的可视化爬虫 https://github.com/scrapinghub/portia


异步任务框架
--------------------------------------
- celery: python 社区一个流行的异步任务框架
- machinery: golang 的异步任务框架 https://github.com/RichardKnop/machinery

端口扫描
--------------------------------------
ZMap: 是密歇根大学研究人员发布的软件，可以在千兆网络条件下 45 分钟完成全网单端口扫描。支持 TCP SYN 、ICMP、UDP 等多种模式。可以用来搜寻代理

后台管理
--------------------------------------
- Ant Design Pro: 基于 react 的后台管理方案，可以用来快速搭建后台运营 or 管理
- flask-admin/Django admin: 框架自带的后台管理。flask-admin 也有类似功能

RPC
--------------------------------------
- thrift: facebook 开源的 rpc 框架，很多大公司在使用
- grpc: grpc是一个高性能、开源和通用的 RPC 框架，面向移动和 HTTP/2 设计。目前提供 C、Java 和 Go 语言版本，分别是：grpc, grpc-java, grpc-go. 其中 C 版本支持 C, C++, Node.js, Python, Ruby, Objective-C, PHP 和 C# 支持. https://github.com/grpc/grpc

Rest
--------------------------------------
- Django Rest Framework: https://www.django-rest-framework.org/
- Flask-Restful: https://flask-restful.readthedocs.io/ ，可以用 cookiecutter-flask-restful 快速启动一个 restful 后端项目
- GRPC: https://github.com/grpc-ecosystem


数据处理和可视化
--------------------------------------
- pandas: 处理报表经常用，非常适合处理矩阵、DataFrame、excel 等。配合一些前端可视化库可以弄报表啥的。碰到  Excel
  处理的强烈建议使用。录了一个小视频讲了下简单的 pands 处理 excel https://zhuanlan.zhihu.com/p/37654682
- matplotlib: python 绘图。数据可视化有很多其他 python 和前端解决方案


压测工具
--------------------------------------
- locust: python实现的压测工具。http://locust.io/， 有 web 界面
- ab
- wrk


Profiler
--------------------------------------
- pyflame: https://github.com/uber/pyflame


数据库工具
--------------------------------------
- mycli: mysql 命令行补全等。https://github.com/dbcli/mycli
- MysqlWorkbench/Sequel Pro: mysql 客户端工具。
- Navicat Premium: 强大的数据库管理工具，收费
- Medis: redis client 工具
- MongoChef: Mongodb 客户端工具

- gen: 根据 mysql 生成 golang gorm model。https://github.com/smallnest/gen
- sqlacodegen: 从 mysql 生成python sqlalchemy model定义。https://github.com/agronholm/sqlacodegen


绘图工具
--------------------------------------
- processon: http://processon.com/ 使用了下感觉还不错，基本能满足需求
- draw.io: https://www.draw.io/

量化投资
--------------------------------------
- tushare: https://github.com/waditu/tushare 有本小白参考书: https://wizardforcel.gitbooks.io/python-quant-uqer/


效率工具
--------------------------------------
- 番茄工作法：人长期专注的时间是有限的，找到适合自己的最佳番茄钟，并且每个时间段都专注于一件事，每件事分清轻重缓急，要事优先。在休息时间处理喝水、上厕所等杂事，做几个深呼吸给脑瓜子充点氧，或者活动下筋骨，眺望下远处。预防职业病（最近有看到工程师视网膜脱落的，要重视身体健康）。
- teambiation/trello: todo list 工具，管理任务。今天做了什么；计划做什么；哪些困难导致工作被阻塞(实在搞不定的记下来及时向同事求助)；发现了什么问题；今天学到了什么。(类似于开发日志之类的玩意，每天都是真正做了事情的，并且最好每天都是学到了新东西的)。有时候一些小灵感或者解决问题的思路在没有纸笔的情况下也能迅速记录到工具里，防止遗忘。
- 主动降噪耳机和音乐：选择类似于《阿尔法波高效记忆音乐》《巴洛克学习音乐》等，能帮助你隔绝噪音，或者你可以在youtube等网站搜索到很多类似工作或学习音乐。反正笔者听歌的时候会想歌词反而会打扰思路，一般就是听这种不怎么让你瞎想的音乐。降噪耳机如果不差钱可以考虑主动降噪耳机(WI1000X/QC30)，效果好一些，网上也有一些对比视频。对于嘈杂的工作环境来说，绝对是一个非常值得的投资。
- 复盘。无论是写代码、做需求、改bug等，事后反思总结。分析并且记录耗时的地方和可以改进的地方(怎么让自己涨点记性，整理 checklist)，对于一些错误或者坑也可以记录成文档当做团队的知识财富。
- zapier: https://zapier.com/ 一个连接 app 自动化工作流的工具，比如可以用来定期提醒发邮件等，非程序员也能实现定时任务啦
- Be Focused: mac 下番茄工作法工具


视频课程录制
--------------------------------------
在 mac 下录制了一些 vim 和 python 的视频教程，记录下使用到的一些硬件和软件工具，硬件工具均可网购，部分软件收费：

- keycastr: mac 按键回显到屏幕，最近录制 vim 视频教程的时候有用到。https://github.com/keycastr/keycastr
- youbute-dl: https://github.com/rg3/youtube-dl 命令行油管视频下载工具
- aria2: https://aria2.github.io/ 轻量级的命令行下载工具
- FFmpeg: 强大的视频处理工具，可以用来截图，截取视频片段等
- ScreenFlow/Camtasia/Obs: 屏幕录制，剪辑工具，收费。笔者用来录制屏幕用
- TunesKit Video Cutter(mac): 视频分割、合并工具
- iZotope RX6: 音频降噪工具，去除杂音、呼吸声等等，收费
- Audacity: 音频处理 https://www.audacityteam.org/download/
- MediaInfo: 查看视频信息
- HandBrake: 视频压缩工具，免费工具。直接从录屏工具导出的视频体积可能会非常大，推荐压缩后上传到网站
- SketchBookPro/Deskscribble(收费): 白板/黑板工具，配合 wacom 手写板可以把屏幕当成黑板或者白板使用。模仿可汗学院的授课方式，笔者在讲述 Python 算法的课程里使用到。
- Wacom 手绘板：用来实现屏幕手写，配合绘图或者白板软件当做黑板使用，方便手写做一些演示或者推导。
- Blue yeti电容麦/铁三角Atr2100动圈麦：使用 mac 内置麦克风音频效果比较差，可以考虑专业的播客级麦克风，录制出来的视频声音要清楚很多。usb 麦克风即插即用，非常方便。动圈麦在嘈杂环境表现更好，不会收录杂音。
- VideoScribe: 制作手绘风格视频，提升视频趣味性。收费
- 课件制作: PowerPoint, Keynote, AxeSlide, Focusky 等
- Mousepose: 鼠标高亮增强工具。演示的时候可以高亮部分区域，其他部分置灰
- 免费字体：思源字体(思源宋体、思源黑体)；站酷字体；


HTML Presentation Tools
--------------------------------------
如果觉得用 ppt 做分享比较老套，可以尝试一些使用 HTML 来做 slide show 的工具。或者使用 markdown 生成 html 幻灯片。

- reveal-md: 使用 markdown 转成网页 slides。https://github.com/webpro/reveal-md 亲测使用起来很方便，依赖 nodejs
- reveal.js: The HTML Presentation Framework
- RISE: 在 jupyter 里做 slide show，甚至可以直接在网页里运行 Python 代码。 https://github.com/damianavila/RISE
- remark: A simple, in-browser, markdown-driven slideshow tool. https://github.com/gnab/remark
- md2googleslides: markdown 转成google slides  https://github.com/gsuitedevs/md2googleslides

* `《HTML-presentation-tools.md》 <https://gist.github.com/PegasusWang/5d00c2e32943f1e3258e964eb64ce4aa>`_
* `《markdown-for-slide-decks.md》 <https://gist.github.com/johnloy/27dd124ad40e210e91c70dd1c24ac8c8>`_


思维导图工具
--------------------------------------

- coggle.it: 一款免费的在线思维导图工具 https://coggle.it/


Linux network debug Tools
--------------------------------------
- ping：特定域名的 ip 是否可达。ping send ICMP echo request

  - ping google.com
  - ping -c 3 google.com

- dig/host: get DNS records。用来替代 nslookup

  - dig google.com 默认返回 A 记录
  - dig google.com MX
  - dig -x 8.8.8.8 反向查询
  - host -a google.com 类似dig
  - host 8.8.8.8

- route: shows and manipulate ip routing table
- traceroute 诊断网络延迟。诊断到目标路径的设备延迟

  - traceroute google.com  命令返回的星号指示丢失包
  - traceroute -n google.com , to avoid reverse dns lookup use -n
  - traceroute -I google.com, send ICMP packet (default UDP, -T TCP, some servers block UDP)

- mtr, realtime tracing
- ss, checking connection performance
- arp, view the arp table
- tcpdump, packet analysis

  - tcpdump -i <network_device> tcp
  - tcpdump -i <network_device> port 80
  - tcpdump -c 20 -i <network_device> port 80 , -c number of events
  - tcpdump -c 20 -i <network_device> src XXX.XXX.XXX.XXX
  - ifconfig, you can obtain the device naems likes this
  - tcpdump -w /path/ -i <network_device>, tcpdump to a file
  - tcmpdump -r /path

- netstat, network statisic,  display connection info, routing table information etc
- lsof: lsof -i:8000

- https://likegeeks.com/linux-network-commands/
- https://unix.stackexchange.com/questions/50098/linux-network-troubleshooting-and-debugging
- https://www.tecmint.com/linux-network-configuration-and-troubleshooting-commands/
