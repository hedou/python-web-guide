==================================
Python/Golang Web 入坑指南
==================================

.. code-block:: text

     ____        _   _                    ______        __        __   _        ____       _     _
    |  _ \ _   _| |_| |__   ___  _ __    / / ___| ___   \ \      / /__| |__    / ___|_   _(_) __| | ___
    | |_) | | | | __| '_ \ / _ \| '_ \  / / |  _ / _ \   \ \ /\ / / _ \ '_ \  | |  _| | | | |/ _` |/ _ \
    |  __/| |_| | |_| | | | (_) | | | |/ /| |_| | (_) |   \ V  V /  __/ |_) | | |_| | |_| | | (_| |  __/
    |_|    \__, |\__|_| |_|\___/|_| |_/_/  \____|\___/     \_/\_/ \___|_.__/   \____|\__,_|_|\__,_|\___|
           |___/

本指南根据作者的自学和工作经历提供(吐槽)一下python/golang
web的学习路线，主要包括概念介绍，参考书籍，开发工具和开发流程等，希望可以帮助非科班人士通过自学入门python
网站开发，弥补学校教育和公司需求之间的鸿沟(也作为自己的学习笔记和面试参考手册)，同时也希望可以作为公司菜鸟实习生的培训手册，帮助公司快速培训新人上手开发，减轻招聘压力。
笔者目前能力有限，希望有经验的python圈人士可以一起协作。
本小书灵感来自于 requests 库作者的 `python-guide <https://github.com/kennethreitz/python-guide>`_ 。
你可以使用强大的电子书阅读软件 `calibre <https://calibre-ebook.com/>`_ 下载epub格式阅读。

如果您感兴趣，也可以参考慕课网教程 `《Python服务端工程师就业面试指导》 <https://coding.imooc.com/class/318.html>`_ 。
本课程提供了详细的Python后端知识大纲和常考面试题，帮助自学的同学就业。

注意：Python 不适合工程管理不完善的团队构建大型项目。如果贵团队没有编码规范、单元测试、静态检测、持续集成、文档注释中的一个或者几个，请慎用动态语言。
如果因为某些网络原因打不开 readthedoc 网站，您可以参考下方快速上手使用 sphinx 本地构建电子书访问。


.. image:: https://readthedocs.org/projects/z42/badge/?version=latest

.. code-block:: python

    # 快速上手构建本地电子书
    # 使用方式 1
    # 本项目页面托管在 readthedoc，如果国内因为网络原因打不开，可以使用如下方式在本地构建
    git clone https://github.com/PegasusWang/python-web-guide.git    # 协作请fork一份你自己的地址
    pip install -r requirements.txt # 安装 Sphinx==1.3.4
    make html   # 构建 html 电子书，之后会在本地生成一个 _build/html 文件夹
    cd _build/html   # 切换到构建好的 html 静态文件夹里
    python3 -m http.server  # 启动一个本地文件服务器，或者 python2 用 python -m SimpleHTTPServer
    # 之后打开 http://127.0.0.1:8000/ 即可本地访问电子书

    # 方式2(推荐)：加入 sphinx-autobuild 自动编辑刷新
    git clone https://github.com/PegasusWang/python-web-guide.git    # 协作请fork一份你自己的地址
    pip install -r requirements.txt  # 安装 Sphinx==1.3.4, sphinx-autobuild
    make serve  # 之后打开 http://127.0.0.1:8000/ 即可本地访问电子书，编辑保存直接自动刷新浏览器

文档采用rst格式书写，用 `readthedocs <https://readthedocs.org/>`_ 托管。一个快速的rst语法demo `教程 <http://azuwis.github.io/sphinx_demo/demo.html>`_。 如果使用vim编写可以使用rst插件 `riv.vim <https://github.com/Rykka/riv.vim>`_ 配合 `InstantRst <https://github.com/Rykka/InstantRst>`_ 本地预览，定期pull一下拉取更新。
欢迎你fork一份然后添加自己的章节，本书主要面对经验尚浅的同学作为自学的指导手册，并非速成指南，内容来自笔者日常学习和工作经验总结。
本电子版书集合了同事的智慧结晶，非常感谢你们带我入坑。
本指南同时会有一些不负责任的吐槽。学到东西的请狂点 star，让笔者有动力更新更多业界实战干货，更多技术分享请关注作者知乎帐号 `pegasuswang <https://www.zhihu.com/people/pegasus-wang/activities>`_ ，知乎专栏 `Python 学习之路 <https://zhuanlan.zhihu.com/python-web>`_ ，`个人博客 <http://ningning.today/>`_ 。
笔者还维护了一个 vim 视频教程专栏，感兴趣可以访问 `玩转vim <https://zhuanlan.zhihu.com/vim-video>`_

TODO:
=================================================================
您的微信打赏就是我写作的最大动力，呵呵哒！


.. raw:: html

   <center>
    <img src="https://user-images.githubusercontent.com/4470478/47126924-74800900-d2bd-11e8-8f24-6d03ddafbc07.png" alt="微信打赏" width=260 height=300>
   </center>
