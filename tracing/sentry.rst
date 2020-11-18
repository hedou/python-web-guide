.. _sentry:

==================================================
Sentry 搭建
==================================================

Sentry 简介
-----------
Sentry 是一个 Python 社区中使用广泛的错误收集系统，可以用来收集并归类 Python 异常/Go error 等信息，在知乎/腾讯内部等都有使用。
你可以把它类比成错误收集的 ELK，虽然它也可以展示收集的日志，不过出于性能等因素考虑，一般只用来收集错误和异常，通过官方提
供的开源版本，可以很容易使用 docker 运行它。

- 官网：https://sentry.io/welcome/
- 优势：相比在浩如烟海的日志中搜索异常信息，使用 Sentry 你可以更加容易发现程序中的严重错误并及时修复，同时附带上有用的上
  下文信息帮助你排查异常。

安装 Docker 和 Docker Compose
-----------------------------

.. code:: sh

   curl -sSL https://get.daocloud.io/docker | sh

   sudo easy_install pip
   sudo pip3 install docker-compose==1.24.1 -i https://pypi.doubanio.com/simple 
   sudo pip3 install docker-compose -i https://pypi.doubanio.com/simple 

pip 如果安装某个包有问题：

``sudo pip install requests --ignore-installed requests``

DockerHub 加速器
================

参考：https://cloud.tencent.com/document/product/457/9113

``sudo vi /etc/docker/daemon.json``

.. code:: json

   {
      "registry-mirrors": [
          "https://mirror.ccs.tencentyun.com"
     ]
   }

重启docker

.. code:: sh

   sudo systemctl daemon-reload
   sudo systemctl restart docker

使用 ``docker info`` 检查 Registry Mirrors 是否正确。

使用官方安装脚本
----------------

https://develop.sentry.dev/self-hosted/

.. code:: sh

   wget https://github.com/getsentry/onpremise/archive/20.11.0.zip
   unzip 20.11.0.zip
   cd onpremise-20.11.0/
   sudo ./install.sh

启动服务
--------

::

   # https://develop.sentry.dev/self-hosted/#productionalizing
   # 启动服务
   docker-compose up -d

   # 重启
   docker-compose restart web worker cron sentry-cleanup

   # 或者直接重启所有服务
   docker-compose restart
