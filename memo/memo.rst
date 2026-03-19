.. _memo:

个人技术备忘录 💻
=====================================================================

..

  好记性不如烂笔头. - 中国谚语


Python
---------------------------------------------------------------
.. code-block:: python

   # python起文件服务器
   python3.4 -m http.server
   python -m SimpleHTTPServer    # python2

   python -u script.py   # 刷新缓冲，执行脚本重定向结果到文件时候比较有用
   python -u script.py  | tee script.log # 打印脚本结果，同时输出到日志 (无缓冲可以立马看到输出)

   # logging
   FATAL(50) > ERROR(40) > WARNING(30) > INFO(20) > DEBUG(10)

   # 使用virtualenv制定python版本
   virtualenv -p /usr/bin/python2.7 ENV2.7

   # pyenv 安装多个版本的 python : https://github.com/pyenv/pyenv
   # pyenv-virtualenv https://github.com/pyenv/pyenv-virtualenv
   # mac install multiple version of python
   brew install pyenv pyenv-virtualenv
   pyenv install 3.6.4  # install python3.6.4
   pyenv virtualenv 3.6.4 v3.6.4
   pyenv activate v3.6.4
   pyenv deactivate

   # 格式化 json，这个可以配置在 vim 里用来格式化当前 json 文本
   cat some.json | python -m json.tool


pip/easy_install
---------------------------------------------------------------
.. code-block:: python

   # 服务器上有时候没有 root 权限可以试试
   # https://stackoverflow.com/questions/7465445/how-to-install-python-modules-without-root-access

   # https://stackoverflow.com/questions/12332975/installing-python-module-within-code
   # pip install
   import pip

   def install(package):
       pip.main(['install', package])

   # Example
   if __name__ == '__main__':
       install('argh')


有时候 pip 安装会比较慢可以更换为豆瓣的源，两种方式

1. ``pip install redis -i https://pypi.doubanio.com/simple``
2. 或者修改全局 ``vi ~/.pip/pip.conf``

.. code-block:: shell

  [global]
  timeout = 60
  index-url = http://pypi.douban.com/simple
  trusted-host = pypi.douban.com


uv 一个用 Rust 编写的极速 Python 包和项目管理器。
---------------------------------------------------------------

.. code-block:: sh

   # 安装(linux/mac)
   curl --proto '=https' --tlsv1.2 -LsSf https://gh-proxy.com/https://github.com/astral-sh/uv/releases/download/0.9.11/uv-installer.sh | sh
   # 初始化项目
   uv init .
   # 创建虚拟环境
   uv venv
   # 导入 requirements.txt 依赖
   uv add -r requirements.txt
   # 添加新的依赖
   uv add requests
   # 删除依赖
   uv remove requests
   # 运行
   uv run main.py


IPython
---------------------------------------------------------------

.. code-block:: sh

   # ipython 如何使用 autoreload，每次重新修改了文件都得重新重启 ipython 很麻烦，解决方式
   # https://support.enthought.com/hc/en-us/articles/204469240-Jupyter-IPython-After-editing-a-module-changes-are-not-effective-without-kernel-restart
   # https://stackoverflow.com/questions/1254370/reimport-a-module-in-python-while-interactive
   # http://ipython.readthedocs.io/en/stable/config/extensions/autoreload.html
   In [1]: %load_ext autoreload
   In [2]: %autoreload 2


.. code-block:: python3

   # -*- coding: utf-8 -*-

   # ~/.ipython/profile_default/startup/startup.py
   # Ned's .startup.py file    ipython 启动加载文件，用来导入一些自定义函数或者模块，方便调试
   # http://stackoverflow.com/questions/11124578/automatically-import-modules-when-entering-the-python-or-ipython-interpreter

   print("(.startup.py)")

   import datetime as dt
   import os
   import pprint
   import re
   import sys
   import time
   import json
   import requests as req

   try:
       import matplotlib.pyplot as plt
       import pandas as pd
       from pandas import Series, DataFrame
       import numpy as np
   except ImportError:
       pass

   print("(imported datetime, os, pprint, re, sys, time, json)")

   def _json_dumps(dict_data, indent=4):
       """用来处理一些包含中文的 json 输出"""
       print(json.dumps(dict_data, indent=indent, ensure_ascii=False))

   def _repr_dict(d):
       """https://stackoverflow.com/questions/25118698/print-python-dictionary-with-utf8-values"""
       print('{%s}' % ',\n'.join("'%s': '%s'" % pair for pair in d.iteritems()))

   def _json_dumps(dict_data, indent=4):
       """用来处理一些包含中文的 json 输出"""
       print(json.dumps(dict_data, indent=indent, ensure_ascii=False))


   repr_dict = _repr_dict
   pp = pprint.pprint
   json_dumps = _json_dumps

.. code-block:: sh

   # http://shawnleezx.github.io/blog/2015/08/03/some-notes-on-ipython-startup-script/
   """
   !!! 注意，如果遇到了 TypeError: super(type, obj): obj must be an instance or subtype of type
   请禁用 autoreload, http://thomas-cokelaer.info/blog/2011/09/382/
   """
   from IPython import get_ipython
   ipython = get_ipython()

   # ipython.magic("pylab")
   ipython.magic("load_ext autoreload")
   ipython.magic("autoreload 2")

   # Ipython 技巧，如何查询文档，比如 time.time 方法的文档
   # https://jakevdp.github.io/PythonDataScienceHandbook/01.01-help-and-documentation.html
   >>> import time
   >>> time.time?  # 回车之后可以输出该函数的 docstring 文档
   >>> time.time??  # 回车之后可以输出该函数的定义


Ipdb
---------------------------------------------------------------
.. code-block:: sh

   # ~/.pdbrc
   # https://github.com/gotcha/ipdb/issues/111

   import os
   alias kk os._exit(0)    # 如果不幸在循环里打了断点，可以用 os._exit(0) 跳出

   alias pd for k in sorted(%1.keys()): print "%s: %s" % (k, (%1[k]))

   # https://stackoverflow.com/questions/21123473/how-do-i-manipulate-a-variable-whose-name-conflicts-with-pdb-commands
   # 如果 pdb 里的内置命令和内置函数冲突了，可以加上 ! 使用内置函数
   !next(iter)

Chrome(Mac)
---------------------------------------------------------------
.. code-block:: sh

   # 使用 comamnd + l 可以立即定位到 url 输入框
   # 使用 vimium 或者 surfingkeys 插件可以用 vim 的模式操作 chrome
   # 用 vimium 如何不用鼠标从 url 输入框回到网页:
   https://superuser.com/questions/324266/google-chrome-mac-set-keyboard-focus-from-address-bar-back-to-page/324267#324267
   https://xavierchow.github.io/2016/03/07/vimium-leave-address-bar/
   # 清理 dns cache, https://superuser.com/questions/203674/how-to-clear-flush-the-dns-cache-in-google-chrome
   Navigate to chrome://net-internals/#dns # and press the "Clear host cache" button.

   # 收藏夹。注意分类收藏，否则后来会收藏多了比较乱。使用 surfingkeys ab (add bookmark) 和 gb(收藏夹管理) 可以快速操作

   # 黑科技：如何chrome 证书认证（不推荐）
   在网页中输入 thisisunsafe 或者 badidea 就可以了

   # surfingkeys 插件可以通过T 搜索当前打开的标签，但是打开数量在 tabsThreshold 以下的时候只会高亮 tab 不会打开搜索框
   # 可以设置这个值 tabsThreshold 只要打开超过该数量的标签就可以使用搜索浏览器标签的功能
   settings.tabsThreshold = 5;

MacOS
---------------------------------------------------------------
.. code-block:: python

   # NOTE: 使用『时间机器』定期备份你的mac 是一个好习惯，笔者买了一个移动硬盘用来定期备份
   # 文件字符串批量替换，git项目里替换的时候注意指定文件类型，防止破坏git信息
   # 注意 mac 的 sed 命令和 linux 有区别(比如mac sed -i 后必须有参数) https://unix.stackexchange.com/questions/13711/differences-between-sed-on-mac-osx-and-other-standard-sed
   # 可以使用 brew install gnu-sed 替换，然后使用 gsed 命令替代
   find . -name \*.py -exec sed -i '' 's/old/new/g' {} \;
   # copy that data into the system’s paste buffer
   cat file.txt | pbcopy
   # The pbpaste command lets you take data from the system’s paste buffer and write it to standard out.
   pbcopy < birthday.txt
   pbpaste | ag name
   pbpaste > filename

   # updatedb https://superuser.com/questions/109590/whats-the-equivalent-of-linuxs-updatedb-command-for-the-mac
   sudo /usr/libexec/locate.updatedb

   # homebrew 更换源, https://maomihz.com/2016/06/tutorial-6/
   cd /usr/local
   git remote set-url origin git://mirrors.ustc.edu.cn/brew.git

   cd /usr/local/Library/Taps/homebrew/homebrew-core
   git remote set-url origin git://mirrors.ustc.edu.cn/homebrew-core.git

   # 从终端查 wifi 密码, https://apple.stackexchange.com/questions/176119/how-to-access-the-wi-fi-password-through-terminal
   security find-generic-password -ga "ROUTERNAME" | grep "password:"

   # XXX.APP已损坏,打不开.你应该将它移到废纸篓 MACOS 10.12 SIERRA，允许“任何来源” https://zhuanlan.zhihu.com/p/135948430
   sudo spctl --master-disable

   # 使用 mounty 挂载 ntfs 盘，Item "file.mov" is used by Mac OS X and cannot be opened.
   # https://apple.stackexchange.com/questions/136157/mov-file-in-external-hd-greyed-out-and-wont-open-this-item-is-used-by-mac-o?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa
   cd /Volumes/[drive name]
   xattr -d com.apple.FinderInfo *
   # or
   SetFile -c "" -t "" path/to/file.mov

   # mac 使用命令挂载
   diskutil mount /dev/disk1s2
   diskutil unmount /dev/disk1s2

   # 使用 rmtrash 删除到 trash，防止危险的 rm 删除命令找不回来。在 bashrc or zshrc alias rm='rmtrash '
   # 如果是 linux 用户，可以使用 safe-rm https://github.com/kaelzhang/shell-safe-rm
   # 删除的文件会放到 $HOME/.Trash 方便恢复
   brew install rmtrash  # npm install -g safe-rm; alias rm='safe-rm'

   # 如何在文件更新之后自动刷新浏览器，需要首先 pip 安装 when-changed
   alias flush_watch_refresh_chrome=" when-changed -v -r -1 -s ./ osascript -e 'tell application \"Google Chrome\" to tell the active tab of its first window to reload' "

   # 如何启用三指拖移(新版本把改设置移动到了辅助功能，使用三指移动可以方便地移动窗口，一般我会启用提高效率)
   辅助功能 -> 鼠标与触控板 -> 触控板选项 -> 启用拖移 (之后就能直接三指翻译单词了)

   # 如何解决 mac 突然没有声音的问题(系统 bug，音频守护进程 coreaudiod出了问题)
   sudo killall coreaudiod

   # mac 如何使用 realpath 显示绝对路径, https://stackoverflow.com/questions/3572030/bash-script-absolute-path-with-os-x
   # brew install coreutils
   grealpath file

   # mac trackpad 蓝牙频繁掉线问题。尝试使用 5G wifi 而不是 2.4G
   # https://apple.stackexchange.com/questions/321948/why-does-my-magic-trackpad-2-randomly-disconnect-and-stop-clicking

   # 软件：pathfinder 如何增加 隔空投送 airdrop 分享文件
   https://support.cocoatech.com/discussions/problems/126873-full-airdrop-sharing-is-here-for-pf8-and-pf7

   # mac 压缩之后去掉 "_MACOSX" 隐藏文件。https://stackoverflow.com/questions/10924236/mac-zip-compress-without-macosx-folder
   zip -d filename.zip __MACOSX/\*

   # 解压 windows zip 乱码。https://www.jianshu.com/p/460f9307dadf
   brew install unar
   unar -e GBK xx中文xx.zip

   # 删除旧文件 https://tecadmin.net/delete-files-older-x-days/
   find /var/log -name "*.log" -type f -mtime +30 # 找到 30 天之前修改的文件，指定文件类型 为 log
   find /var/log -name "*.log" -type f -mtime +30 -delete  # 执行删除
   find /opt/backup -type f -mtime +30

   # hide or show desktop icon for presentation 隐藏桌面图标
   alias hide_desktop_icon='defaults write com.apple.finder CreateDesktop -bool false; killall Finder'
   alias show_desktop_icon='defaults write com.apple.finder CreateDesktop -bool true; killall Finder'

   # mac https://apple.stackexchange.com/questions/54329/can-i-get-the-cpu-temperature-and-fan-speed-from-the-command-line-in-os-x
   gem install iStats # need
   istats all

   # mac 休眠 https://www.jianshu.com/p/ec888c3e33dd
   sudo shutdown -s +10 # 10分钟后休眠

   # mac https://superuser.com/questions/273756/how-to-change-default-app-for-all-files-of-particular-file-type-through-terminal
   # https://chainsawonatireswing.com/2012/09/19/changing-default-applications-on-a-mac-using-the-command-line-then-a-shell-script/
   brew install duti # 安装 duti
   osascript -e 'id of app "calibre.app"' # https://www.hexnode.com/mobile-device-management/help/how-to-find-the-bundle-id-of-an-application-on-mac/
   duti -x sh # 获取 .sh 文件的默认打开软件，最后一行是id
   duti -s io.brackets.appshell .md all # 用该 id 的软件打开所有的 md 文件

   osascript -e 'id of app "ebook-viewer.app"' # 安装 calibre 之后，找到附带的电子书浏览软件 id
   duti -s com.calibre-ebook.ebook-viewer .mobi all # 用 ebook-viewer 打开所有的 mobi

   # legacyScreenSaver memory 内存占用高。装了 fliqlo 翻页时钟屏保， 尝试重新装下 https://fliqlo.com/screensaver/ 最新版

如何发送 mac 通知，可以用来做提示

.. code-block:: python

   # https://stackoverflow.com/questions/17651017/python-post-osx-notification
   # 配合 crontab 可以用来做一个简单的定时任务提醒功能 57-59 17 * * * python ~/.tmp/noti.py


   # ~/.tmp/noti.py
   import os

   def notify(title, text):
       os.system(""" osascript -e 'say "家里放点音乐吧"' """)
       os.system(""" osascript -e 'display notification "{}" with title "{}"' """.format(text, title))

   notify("开会啦", "Go Go Go !!!")

增加终端下光标的移动速度(⭐️ 非常好用)：

.. code-block:: shell

   # mac: 系统设置-> 键盘 -> 修改按键重复到最快，重复前延迟最短。可以让光标在终端里移动更快 (推荐下边的命令修改更快)

   # 增加 terminal 光标移动速度, https://stackoverflow.com/questions/4489885/how-can-i-increase-the-cursor-speed-in-terminal
   # 终端执行以下三个 defaults 命令后必须重启(亲测有效，速度飞起! 😄) https://medium.com/@juanpaulo/set-blazingly-fast-key-repeats-a87c808ad01d

   # Disable press-and-hold for keys in favor of key repeat
   defaults write NSGlobalDomain ApplePressAndHoldEnabled -bool false
   # Set a blazingly fast keyboard repeat rate
   defaults write NSGlobalDomain KeyRepeat -int 1  # 默认值 2，设置成 1 合适，设置成 0 就太快了，翻页刷新有问题
   defaults write NSGlobalDomain InitialKeyRepeat -int 10

如何命令行格式化u盘:

.. code-block:: shell

   # https://superuser.com/questions/527657/how-do-you-format-a-2-gb-sd-card-to-fat32-preferably-with-disk-utility

   # 找到你的u 盘
   diskutil list
   # 格式化 u 盘，注意 UDISKNAME 必须大写。最后的 /dev/disk2 是上一步找到的 u 盘，千万别写错了
   sudo diskutil eraseDisk FAT32 UDISKNAME MBRFormat /dev/disk2

增加 time machine 备份速度:

.. code-block:: shell

   # https://huataihuang.gitbooks.io/cloud-atlas/content/develop/mac/time_machine_backup_speed.html
   sudo sysctl debug.lowpri_throttle_enabled=0 # 关闭限流
   sudo sysctl debug.lowpri_throttle_enabled=1 # 恢复限流


SSH
-------------

二次验证自动登录跳板机脚本，根据你的密码和服务器配置修改即可。

.. code-block:: python

  #!/bin/sh

  # 有二次验证登录跳板机的时候比较麻烦，可以用这个脚本自动登录跳板机 参考：https://juejin.im/post/5ce760cef265da1b6e657d6f
  # brew install expect
  # brew install oath-toolkit
  # {user} {ip} {yourpassword} {server_qr_token} 替换成对应的 用户名、ip、密码、服务器秘钥 (密码建议定期更换防止安全风险)
  export LC_CTYPE="en_US.UTF-8"
  expect -c "
  spawn ssh user@ip -p22
  set timeout 3
  expect  \"user@ip's password:\"
  set password yourpassword
  set token \"`oathtool --totp -b -d 6 server_qr_token`\"
  send \"\$password\$token\r\"
  interact
  "


Mac 蓝牙耳机(自用索尼 wi1000x)
---------------------------------------------------------------
如何给 Macbook 开启 Apt-X 蓝牙音质果更高

- https://www.jianshu.com/p/a1efa561ed9e
- https://gist.github.com/dvf/3771e58085568559c429d05ccc339219

注意：mac有一个 bug 至今没有修复，cpu 占用高的时候使用蓝牙耳机可能会被莫名其妙修改平衡。声音一边大一边小，去设置-声音里调整一下就好。

`macbook-pro-bluetooth-audio-balance-keeps-changing-by-itself <https://apple.stackexchange.com/questions/280145/macbook-pro-bluetooth-audio-balance-keeps-changing-by-itself>`_


Proxy
---------------------------------------------------------------

mac电脑下设置socks5代理 https://blog.csdn.net/fafa211/article/details/78387899


Oh My Zsh
---------------------------------------------------------------
.. code-block:: shell

   # Powerlevel9k 是一个强大的 zsh 主题
   # iTerm2 + Oh My Zsh + Solarized color scheme + Meslo powerline font + [Powerlevel9k] - (macOS)
   # https://gist.github.com/kevin-smets/8568070

   # https://gist.github.com/dogrocker/1efb8fd9427779c827058f873b94df95
   # 安装自动补全插件
   git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions
   git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
   # nvi ~/.zshrc
   plugins=(git zsh-autosuggestions zsh-syntax-highlighting)

   # 如何复制上一条命令, https://apple.stackexchange.com/questions/110343/copy-last-command-in-terminal
   alias lcc='fc -ln -1 | awk "{\$1=\$1}1" ORS="" | pbcopy '

   # 报错：_git:58: _git_commands: function definition file not found
   # 解决方式：rm ~/.zcompdump*; rm ~/.zplug/zcompdump  # https://github.com/robbyrussell/oh-my-zsh/issues/3996
   # rm ~/.zcompdump; exec zsh -l  # https://github.com/ohmyzsh/ohmyzsh/issues/3996

Linux(centos/ubuntu)
---------------------------------------------------------------

.. code-block:: python

    # 查看版本
    lsb_release -a

    # virtual box虚拟机和windows主机共享目录方法：安装增强工具；win主机设置共享目录例如ubuntu_share；在ubuntu里建立/mnt/share后使用命令：

    sudo mount -t vboxsf ubuntu_share /mnt/share/

    # 映射capslock 为　ctrl
    setxkbmap -layout us -option ctrl:nocaps

    # 文件字符串批量替换(危险操作最好有 git 版本控制方便回滚)
    grep oldString -rl /path | xargs sed -i "s/oldString/newString/g"

    # 删除多个文件中包含 patter 的指定行。比如递归删除包含 sleep 的行 (注意这种危险操作最好有 git 版本控制，方便回滚)
    # https://stackoverflow.com/questions/1182756/remove-line-of-text-from-multiple-files-in-linux
    find . -name "*.go" -type f | xargs sed -i -e '/sleep/d'

    # 递归删除某一类型文件
    find . -name "*.bak" -type f -delete

    # 监控某一日志文件变化
    tail -f t.log

    # 类似mac pbcopy, apt-get install xsel
    cat README.TXT | xsel
    cat README.TXT | xsel -b # 如有问题可以试试-b选项
    xsel < README.TXT
    # 将readme.txt的文本放入剪贴板

    xsel -c
    # 清空剪贴板

    # 可以把代码文件贴到paste.ubuntu.com共享，此命令返回一个网址
    # sudo apt-get install pastebinit; sudo pip install configobj
    pastebinit -i [filename]


    # json格式化输出
    echo '{"foo": "lorem", "bar": "ipsum"}' | python -m json.tool
    python -m json.tool my_json.json
    # 或者apt-get intsall jq
    jq . <<< '{ "foo": "lorem", "bar": "ipsum"  }'


    # 进程相关
    dmesg | egrep -i -B100 'killed process'   # 查看被杀死进程信息
    # linux 批量杀掉筛选进程(比如定时脚本多个同时执行，最好限制) https://blog.csdn.net/weiyichenlun/article/details/59108463
    ps -ef | grep main.py | grep -v grep | awk '{print $2}' | xargs kill -9

    # scp
    scp someuser@192.168.199.1:/home/someuser/file ./    # 远程机器拷贝到本机
    scp ./file someuser@192.168.199.1:/home/someuser/    # 拷贝到远程机器

    # tar
    tar zxvf FileName.tar.gz    # 解压
    tar zcvf FileName.tar.gz DirName    # 压缩

    # 监控文件变动并且自动增量同步本地文件到服务器对应文件夹（需要先安装 when-changed)
    when-changed -r -v -1 . rsync -avh --exclude='.git/' --exclude='mydoc/' --exclude='output/' /Users/pegasus/work/code/ XXX@ip:/home/pegasus/work/code/


代码搜索用Ag(the silversearcher)/rg, 比ack快

.. code-block:: shell

    # 安装
    sudo apt-get install silversearcher-ag    # ubuntu
    brew install ag # mac

    ag string dir/    # search dir
    ag readme$    # regular expression
    ag -Q .rb    # Literal Expression Searches, search for the exact pattern。这个选项很有用，特殊字符不用转义了
    ag string -l    # Listing Files (-l)
    ag string -i    # Case Insensitive Searches (-i)
    ag string -G py$    # 搜索应py结尾的文件 (指定文件类型)
    ag readme -l --ignore-dir=railties/lib    # 忽略文件夹
    ag readme -l --ignore-dir="*.rb"    # 忽略特性类型文件
    .agignore    # 用来忽略一些vcs，git等文件。

Centos
-------------------------------------------------------------

.. code-block:: shell

   # 如何搜索和安装指定版本
   # https://unix.stackexchange.com/questions/151689/how-can-i-instruct-yum-to-install-a-specific-version-of-package-x
   yum --showduplicates list golang
   yum install package-version

crontab
-------------------------------------------------------------
分、时、日、月、周

.. code-block:: python

    # 记得bashrc里边
    EXPORT EDITOR=vim
    export PYTHONIOENCODING=UTF-8

    # crontab注意：绝对路径；环境变量；
    0 */5 * * * python -u /root/wechannel/crawler/sougou_wechat/sougou.py >> /root/wechannel/crawler/sougou_wechat/log 2>&1
    */5 * *  * * /root/pyhome/crawler/lagou/changeip.sh >> /root/pyhome/crawler/lagou/ip.log 2>&1

    # 一个 crontab 表达式工具
    - https://tooltt.com/crontab/
    - https://tool.lu/crontab/


可以用如下方式执行依赖其他模块的python脚本，用run.sh执行run.py，记得chmod +x可执行权限，运行前执行下sh脚本测试能否成功

.. code-block:: sh

    #!/usr/bin/env bash
    PREFIX=$(cd "$(dirname "$0")"; pwd)
    cd $PREFIX
    source ~/.bashrc

    python -u run.py    # -u 参数强制刷新输出
    date


对于python脚本，可以用如下方式保证同一时间只有一个脚本在运行（一些定时任务同一台机器上多个同时跑可能有问题），可以用
如下方式限制。（多个机器上应该用分布式锁）

.. code-block:: shell

    #!/usr/bin/env python
    # -*- coding:utf-8 -*-

    import time
    # https://stackoverflow.com/questions/380870/make-sure-only-a-single-instance-of-a-program-is-running
    # 更好的方式使用 tendo
    # pip install tendo
    from tendo import singleton
    me = singleton.SingleInstance() # will sys.exit(-1) if other instance is running

    def main():
        time.sleep(10)
        print(time.time())

    if __name__ == '__main__':
        main()


* `《crontab快速参考》 <http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html>`_


Iterm2/Terminal
-------------------------------------------------------------

.. code-block:: sh

   # https://stackoverflow.com/questions/11913990/iterm2-keyboard-shortcut-for-moving-tabs-around
   # Preferences/Keys 自定义配置使用 Cmd +jk 来在 Iterm2 tab 前后移动，模仿 vim 键位

   # 如何防止 command+w 意外关闭导致工作丢失，这里可以如下设置，每次关闭提醒
   # Settings -> Profiles -> Session -> Prompt before closing 勾选 Always

   # 如何使用 rz/sz 传文件
   https://segmentfault.com/a/1190000012166969

   # 如何使用 iterm2 it2copy 从 服务器上用 vim 拷贝文件
   # https://stackoverflow.com/questions/10694516/vim-copy-mac-over-ssh/10703012
   1. 安装 iTerm2 Utilities 到服务器。iTerm2 -> Install shell Integratio。后边是 bash or zsh，根据你用的 shell 选择
    curl -L https://iterm2.com/shell_integration/install_shell_integration_and_utilities.sh | zsh
   2. 重新登录之后 it2copy 生效
   3. 在 vim visual 模式选择之后 执行 `:w !it2copy` 即可。或这直接 cat file.txt | it2copy

   # 终端输出乱序。有时候有一些脚本或者软件可能会修改终端配置但是失败后又没有恢复，导致输出乱序，解决如下
   `stty sane` 或者 `reset`


Tmux
-------------------------------------------------------------

.. code-block:: sh

   # 建议直接用 https://github.com/gpakosz/.tmux 这个强大的 tmux 配置(oh-my-tmux)
   # 不过注意，如果一开始 tmux.conf.local 里的命令执行失败（比如curl 网络失败）可能导致插件加载失败，注意排查

   # https://wiki.archlinux.org/index.php/tmux
   tmux rename -t oriname newname
   tmux att -t name -d               # -d 不同窗口全屏

   # 如果手贱在本机tmux里又ssh到服务器又进入服务器的tmux怎么办(退出 tmux 套娃)
   c-b c-b d

   # 如果升级了 tmux 之后，使用 tmux 出现 tmux server exited unexpectedly 尝试删除 /tmp 里的 tmux 临时文件
   # https://github.com/tmux/tmux/issues/2376

   # 技巧：tmux 如何在多个 pane 执行一样的命令。先执行 ctrl + b :
   ctrl + b :
   setw synchronize-panes on
   setw synchronize-panes off

   # Vim style pane selection
   bind -n C-h select-pane -L
   bind -n C-j select-pane -D
   bind -n C-k select-pane -U
   bind -n C-l select-pane -R

   # https://stackoverflow.com/questions/22138211/how-do-i-disconnect-all-other-users-in-tmux
   tmux a -dt <session-name>

   # 如何 ssh 后自动 attach 到某个 session。编辑你的 .bashrc or .zshrc
   if [[ "$TMUX" == "" ]] && [[ "$SSH_CONNECTION" != "" ]]; then
       # Attempt to discover a detached session and attach it, else create a new session
       WHOAMI="lens"   # attach 的 session 名称
       if tmux has-session -t $WHOAMI 2>/dev/null; then
           tmux -2 attach-session -t $WHOAMI
       else
           tmux -2 new-session -s $WHOAMI
       fi
   fi
   # 或者
   if [[ -z "$TMUX" ]] && [ "$SSH_CONNECTION" != "" ]; then
       SESSION_NAME="sessionname"
       tmux attach-session -t $SESSION_NAME || tmux new-session -s $SESSION_NAME
   fi

   # 问题：Tmux resurrect file not found! 。新版本应该是放到了 ~/.local/share/tmux/resurrect 。老版本在~/.tmux/resurrect
   function tmux-resurrect-reset-last() {
       cd ~/.local/share/tmux/resurrect && \
           ln -f -s $(/bin/ls -t tmux_resurrect_*.txt | head -n 1) last && \
           /bin/ls -l last
   }

   # use prefix + m zoom and unzoom panes. https://tao-of-tmux.readthedocs.io/en/latest/manuscript/07-pane.html
   bind-key -T prefix m resize-pane -Z
   # 清理 tmux 备份文件(超过 1 天)
   cd ~/.local/share/tmux/resurrect
   find . -type f -mmin +1440 -name 'tmux_resurrect_*.txt' -exec rm {} \;


SSH
-------------------------------------------------------------

.. code-block:: python

   # https://superuser.com/questions/98562/way-to-avoid-ssh-connection-timeout-freezing-of-gnome-terminal/98565#98565
   Press Enter, ~, . one after the other to disconnect from a frozen session.
   # https://unix.stackexchange.com/questions/176547/copy-only-file-details-file-name-size-time-from-remote-machine-in-unix
   ssh remotemachine  "ls -l /opt/apache../webapps/Context"
   # 使用 paramiko  库可以实现 ssh client 功能
   # https://www.digitalocean.com/community/tutorials/how-to-use-fabric-to-automate-administration-tasks-and-deployments


Fabric
-------------------------------------------------------------
可以用 Fabric 实现一些自动化控制服务器功能。示例 fabfile.py

.. code-block:: python

  # -*- coding: utf-8 -*-
  import os
  from fabric.api import run, env, get, local

  """
  需求：经常忘记开发机 build 完go 二进制文件以后 scp 到本地，导致有时候部署还是老的二进制文件。

  功能：
  实现监听开发机的二进制文件变动，每一次和本地文件对比，如果有开发机二进制文件大小变了，就拷贝到本地来。

  # pip install fabric==1.14.0
  # brew install watch
  mac 下用 watch 用来定期执行命令 watch -n 60 ls

  比如每分钟检查一下开发机上的 FaceFusionServer 是否重新 build 了，然后拉取到本地，可以执行
  watch -n 30 fab monitor_facefusion_server monitor_uploadserver

  1. http://www.bjhee.com/fabric.html
  """

  class Bcolors:
      HEADER = '\033[95m'
      OKBLUE = '\033[94m'
      OKGREEN = '\033[92m'
      WARNING = '\033[93m'
      FAIL = '\033[91m'
      ENDC = '\033[0m'
      BOLD = '\033[1m'
      UNDERLINE = '\033[4m'


  env.hosts = ['dev']
  env.use_ssh_config = True
  env.password = ""


  def who():
       run('whoami')


  def is_change(remote_path, local_path):
       """ 根据 md5 判断是否变化，注意 centos 和 mac 命令和结果格式不同
       centos:
       md5sum UploadServer
       e4fccc07eafc7ef97d436c50546e352b  UploadServer

       mac:
       md5 UploadServer
       MD5 (UploadServer) = e4fccc07eafc7ef97d436c50546e352b

       :param remote_path: absolute remote server path
       :param local_path: local path
       """
       output = run("md5sum {}".format(remote_path))  # 请保证路径存在，不会判断
       remote_md5 = output.split()[0].strip()
       if not os.path.exists(local_path):  # 第一次本地没有文件直接拉取
           return True
       local_output = local("md5 {}".format(local_path), capture=True)
       local_md5 = local_output.split()[-1].strip()
       return remote_md5 != local_md5


  def monitor_uploadserver():
       remote_path = "/user/work/UploadServer"
       local_path = "./UploadServer"
       if is_change(remote_path, local_path):  # 变化了就复制到本地 get(remote, local)，存在会覆盖
           print(Bcolors.WARNING + "===========%s file changed=========" + Bcolors.ENDC)
           get(remote_path, local_path)
           local("chmod +x {}".format(local_path))
       else:
           print(Bcolors.HEADER + local_path + " not change" + Bcolors.ENDC)


Makefile
-------------------------------------------------------------

.. code-block:: sh

   # 如何设置子进程环境变量 https://stackoverflow.com/questions/23843106/how-to-set-child-process-environment-variable-in-makefile
   test: export NODE_ENV = test

Git
-------------------------------------------------------------

.. code-block:: python

    # .gitconfig配置用如下配置可以使用pycharm的diff和merge工具（已经安装pycharm）
    [diff]
        tool = pycharm
    [difftool "pycharm"]
        cmd = /usr/local/bin/charm diff "$LOCAL" "$REMOTE" && echo "Press enter to continue..." && read
    [merge]
        tool = pycharm
        keepBackup = false
    [mergetool "pycharm"]
        cmd = /usr/local/bin/charm merge "$LOCAL" "$REMOTE" "$BASE" "$MERGED"

    # https://stackoverflow.com/questions/34549040/git-not-displaying-unicode-file-names
    # git 显示中文文件名，如果你的文件名有中文会好看很多
    git config --global core.quotePath false

    # 用来review：
    git log --since=1.days --committer=PegasusWang --author=PegasusWang
    git log --since="6am" # 查看当天提交
    git diff commit1 commit2

    # 冲突以后使用远端的版本： NOTE：注意在 git merge 和 git rebase 中 ours/theirs 含义相反!
    # rebase 场景下，theirs 实际表示的是当前分之
    # merge 场景下相反，theirs 表示的是远端分之
    # https://stackoverflow.com/questions/16825849/choose-git-merge-strategy-for-specific-files-ours-mine-theirs
    # https://bitmingw.com/2017/02/16/git-merge-rebase-ours-and-theirs/
    git checkout --theirs templates/efmp/campaign.mako

    # 防止http协议每次都要输入密码：
    git config --global credential.helper 'cache --timeout=36000000'      #秒数

    # 暂存和恢复，当我们需要切分支又暂时不想 git add，可以先把目前的修改暂存起来
    git stash # 暂存当前的修改
    git stash apply
    git stash apply stash@{1}
    git stash pop # 重新应用储藏并且从堆栈中移走
    # 显示 git stash 内容 https://stackoverflow.com/questions/7677736/git-diff-against-a-stash
    git stash list # 展示当前的所有 stash 列表
    git stash show -p  # see the most recent stash
    git stash show -p stash@{1}

    # 删除远程分之
    git push origin --delete {the_remote_branch}

    # 手残 add 完以后输入错了 commit 信息
    git commit --amend
    # 修改文件内容并合并到上一次的commit变更当中。比如发现没有修改完全，又改了代码，可以修改之后 add 然后执行
    git commit --amend --no-edit
    # 类似的还可以修改上一个提交者的名字 https://stackoverflow.com/questions/750172/how-to-change-the-author-and-committer-name-and-e-mail-of-multiple-commits-in-gi
    git config --global user.name "you name"
    git config --global user.email you@domain.com
    git commit --amend --reset-author
    # 如果想要修改多个历史提交的 commit 信息，可以使用 git rebase -i 交互式修改。对应的提交行使用 reword 就可以

    # 撤销 add （暂存），此时还没有 commit。比如 add 了不该 add 的文件
    git reset -- file
    git reset # 撤销所有的 add

    # 撤销修改
    git checkout -- file

    # 手残pull错了分支就(pull是先fetch然后merge)。或者 revert 一个失误的 merge
    git reset --hard HEAD~
    # 如果 pull 产生了 冲突，可以撤销。
    git merge --abort
    # git rebase 同样可以
    git rebase --abort

    # How to revert Git repository to a previous commit?, https://stackoverflow.com/questions/4114095/how-to-revert-git-repository-to-a-previous-commit
    git reset --hard 0d1d7fc32

    # 手残直接在master分之改了并且add了
    git reset --soft HEAD^
    git branch new_branch # 切到一个新分支去 commit
    git checkout new_branch
    git commit -a -m "..."
    # 或者
    git reset --soft HEAD^
    git stash
    git checkout new_branch
    git stash pop

    # 如果改了master但是没有add比较简单，三步走
    git stash
    git checkout -b new_branch
    git stash pop

    # rename branch
    git branch -m <oldname> <newname>
    git branch -m <newname> # rename the current branch

    # 指定文件类型diff
    git diff master -- '*.c' '*.h'
    # 带有上下文的diff
    git diff master --no-prefix -U999

    # undo add
    git reset <file>
    git reset    # undo all
    # undo git reset https://stackoverflow.com/questions/2510276/how-to-undo-git-reset
    git reset 'HEAD@{1}'

    # 查看add后的diff
    git diff --staged

    # http://weizhifeng.net/git-rebase.html
    # rebase改变历史, 永远不要用在master分之，别人有可能使用你的分之时也不要用
    # only change history for commits that have not yet been pushed
    # master has changed since I stared my feature branch, and I want bo bring my branch up to date with master. - Dont't merge. rebase
    # rebase: finds the merge base; cherry-picks all commits; reassigns the branch pointer.
    # then git push -f
    # git rebase --abort

    # 全局 ignore, 对于不同编辑器协作的人比较有用，或者用来单独忽略一些自己建立的测试文件等。
    # NOTE: git 支持每个子文件夹下有一个自己的 .gitignore，文件路径也是相对当前文件夹
    git config --global core.excludesfile ~/.gitignore_global  # 全局忽略一些文件

    # 拉取别人远程分支，在 .git/config 里配置好
    git fetch somebody somebranch
    git checkout -b somebranch origin/somebranch

    # prune all the dead branches from all the remotes
    # https://stackoverflow.com/questions/17933401/how-do-i-remove-deleted-branch-names-from-autocomplete?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa
    git fetch --prune --all # 清理本地本删除的远程分之，补全的时候很干净，没有已经删除的分之

    # https://stackoverflow.com/questions/1274057/how-to-make-git-forget-about-a-file-that-was-tracked-but-is-now-in-gitignore
    # https://wildlyinaccurate.com/git-ignore-changes-in-already-tracked-files/
    # 如果一个文件已经被 git 跟踪但是你之后又不想提交针对它的修改了，可以这么做（比如我想修改一些配置，本地 debug 等）
    git update-index --assume-unchanged <file>    # 忽略一个已经 tracked 的文件，修改后不会被 commit
    git update-index --no-assume-unchanged <file>   # undo 上一步
    # 那如何列出这些文件呢？ https://stackoverflow.com/questions/2363197/can-i-get-a-list-of-files-marked-assume-unchanged
    git ls-files -v | grep '^[[:lower:]]'

    # https://stackoverflow.com/questions/48341920/git-branch-command-behaves-like-less
    # 禁止 git brach 的时候使用交互式
    git config --global pager.branch false

    # git rm file and add, https://stackoverflow.com/questions/9591407/unstage-a-deleted-file-in-git/9591612
    # this restores the file status in the index
    git reset -- <file>
    # then check out a copy from the index
    git checkout -- <file>

    # 如何恢复一个已经删除的分之, https://stackoverflow.com/questions/3640764/can-i-recover-a-branch-after-its-deletion-in-git
    git reflog  # 查找对应 commit hash
    git checkout -b branch-name hash

    # git diff 代码显示 tab 为 4 个空格，比如看 go 代码的时候，git diff 显示 8 个
    # https://stackoverflow.com/questions/10581093/setting-tabwidth-to-4-in-git-show-git-diff
    git config --global core.pager 'less -x1,5'

    # git 如何使用不同的 committer，除了每个项目和全局可以设置 gitconfig 里的 user 外，可以使用如下方式
    # https://stackoverflow.com/questions/4220416/can-i-specify-multiple-users-for-myself-in-gitconfig
    # global config ~/.gitconfig
    [user]
        name = John Doe
        email = john@doe.tld

    [includeIf "gitdir:~/work/"]
        path = ~/work/.gitconfig

    # ~/work/.gitconfig
    [user]
        email = john.doe@company.tld

    # 从提交历史搜索字符串，比如提交历史中引入了一个新的函数，可以通过这个方式搜索
    # https://stackoverflow.com/questions/5816134/how-to-find-the-git-commit-that-introduced-a-string-in-any-branch
    git log -S 'hello world' --source --all

    # 统计xx某某提交了多少代码
    git log --author="xxx" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'

    # 修改上一次提交人。比如一开始 git commiter 配置错了。https://stackoverflow.com/questions/3042437/how-to-change-the-commit-author-for-one-specific-commit
    git commit --amend --author="Author Name <email@address.com>" --no-edit

    # tags 功能(比如从一个源码的 tag 构建) https://stackoverflow.com/questions/35979642/what-is-git-tag-how-to-create-tags-how-to-checkout-git-remote-tags
    git tag -l  # 显示所有 tag
    git checkout tags/<tag> -b <branch>

    # 生成和应用 patch, https://stackoverflow.com/questions/28192623/create-patch-or-diff-file-from-git-repository-and-apply-it-to-another-different
    git diff tag1..tag2 > mypatch.patch
    git apply mypatch.patch

    # 删除当前文件夹 git 未跟踪的文件 https://stackoverflow.com/questions/61212/how-do-i-remove-local-untracked-files-from-the-current-git-working-tree
    git clean -f # 注意危险操作提前确认文件确实可以删除


Git 删除大文件
----------------------------
Git 只适合代码文本管理，如果误提交了一个二进制文件或者视频等文件将会导致 git 仓库变得非常大。
你应该在首次提交代码的时候就是用 gitignore 忽略掉大文件，如果已经误提交了，直接删除文件并不会删除 git 历史中的记录。
需要使用专门的工具来进行删除，目前官方推荐使用 git-filter-repo 了，不再推荐使用 git filter-branch。

.. code-block:: shell

    # ⭐️ git 如何删除提交历史中的大文件(比如很多新手误提交了一个二进制或者视频等大文件)
    # git 注意不要把二进制大文件，视频文件等放入到版本库，可能会导致 .git 非常大，删了也无济于事
    find . -executable -type f >>.gitignore # https://stackoverflow.com/questions/5711120/gitignore-without-binary-files

    # git 历史删除大文件。如果你提交了大文件，即使你git rm删除了也会留在 git 的历史记录中，导致.git 文件夹很大
    # https://stackoverflow.com/questions/8083282/how-do-i-remove-a-big-file-wrongly-committed-in-git
    # git filter-branch --index-filter "git rm -rf --cached --ignore-unmatch path_to_file" HEAD

    # ⭐️ 推荐使用 git-filter-repo
    # https://stackoverflow.com/questions/2100907/how-to-remove-delete-a-large-file-from-commit-history-in-the-git-repository
    # https://www.jianshu.com/p/03bf1bc1b543
    pip install git-filter-repo # 安装
    git filter-repo --invert-paths --path-match YOUR_BIG_FILE  # 从提交历史删除大文件
    git push -f origin master # 因为修改了提交历史，可能需要临时放开一下 master 权限，强行 push 一次


Git工作流
------------

.. code-block:: shell

   git checkout master    # 切到master
   git pull origin master     # 拉取更新
   git checkout -b newbranch    # 新建分之，名称最好起个有意义的，比如jira号等

   # 开发中。。。
   git fetch origin master    # fetch master
   git rebase origin/master    #

   # 开发完成等待合并到master，推荐使用 rebase 保持线性的提交历史，但是记住不要在公众分之搞，如果有无意义的提交也可以用 rebase -i 压缩提交
   git rebase -i origin/master
   git checkout master
   git merge newbranch
   git push origin master

   # 压缩提交
   git rebase -i HEAD~~    # 最近两次提交

Git Worktree
----------------------

.. code-block:: shell

   # 参考 https://zhuanlan.zhihu.com/p/1938293629455152167
   # git Worktree 让一个 git 分支仓库对应多个本地目录，每个目录不同分支。不用反复切分支，多分支并行。结合 claude code 并行开发
   # 格式：git worktree add <新目录路径> <分支名>
   # 示例1：给bug-fix分支创建独立目录（同级目录）。创建目录并且关联分支
   # 先在当前项目创建一个 .wt 目录用作作为 worktree 的容器。可以写到全局  ~/.gitignore_global  忽略这个目录
   # 注意：先切到你的项目代码目录后执行
   mkdir .wt
   # 如果现有了 bugfix 分支直接执行
   git worktree add .wt/bugfix bugfix
   # 否则，使用 -b 参数同时创建分支，比如这里创建两个目录跟踪两个不同分支开发
   git worktree add .wt/bugfix -b bugfix
   git worktree add .wt/feat -b feat
   # 此时 cd .wt 目录下可以看到这两个目录 bugfix 和 feat

   # 查看所有创建目录
   git worktree list

   # 使用方式，直接切到新建的 worktree 某个目录下开发即可
   cd .wt/bugfix
   git add . && git commit -m "修复登录Bug" && git push

   # 删除某个工作区目录（先确保其中无未跟踪变更）
   git worktree remove .wt/feat
   git worktree remove -f .wt/feat # 强制删除，可能有风险

   # 预演清理（只显示将被清理的内容），安全起见先执行这个之后再去掉参数实际执行清理
   git worktree prune -n -v # 先执行
   git worktree prune

   # 收尾：先 remove 再 prune
   git worktree remove .wt/feat
   git worktree prune


Git hook
------------
比如我们要在每次 commit 之前运行下单测，进入项目的 .git/hooks 目录， "cp pre-commit.sample pre-commit" 修改内容如下:

.. code-block:: bash

    #!/bin/sh

    if git rev-parse --verify HEAD >/dev/null 2>&1
    then
        against=HEAD
    else
        # Initial commit: diff against an empty tree object
        against=4b825dc642cb6eb9a060e54bf8d69288fbee4904
    fi

    # Redirect output to stderr.
    exec 1>&2

    if /your/path/bin/test:    # 这里添加需要运行的测试脚本
    then
        exit 0
    else
        exit 1
    fi

    # If there are whitespace errors, print the offending file names and fail.
    exec git diff-index --check --cached $against --


Gitub
------------
克隆 Github 仓库时遇到报措 kex_exchange_identification: Connection closed by remote host。执行 ``ssh -T git@github.com``
kex_exchange_identification: Connection closed by remote host。 可能是因为某些🪜封禁了 github 端口 22 的连接。修改端口:

.. code-block:: bash

  Host github.com
      HostName ssh.github.com
      User git
      Port 443


vim/neovim
-------------

.. code-block:: vim

    " http://stackoverflow.com/questions/9104706/how-can-i-convert-spaces-to-tabs-in-vim-or-linux
   :set tabstop=2      " To match the sample file
   :set noexpandtab    " Use tabs, not spaces
   :%retab!            " Retabulate the whole file，替换tab为空格
   map <F4> :%retab! <CR> :w <CR> " 映射一个命令

   "https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjF6JzH8aTRAhXiqVQKHUQBDcIQFggcMAA&url=http%3A%2F%2Fstackoverflow.com%2Fquestions%2F71323%2Fhow-to-replace-a-character-by-a-newline-in-vim&usg=AFQjCNGer9onNl_RExCUdE75ctTvVx8WGA&sig2=WrcRh9RFNvN6bUZoHpJvDg
   "vim替换成换行符使用\r不是\n
   " 多行加上引号 http://stackoverflow.com/questions/9055998/vim-add-tag-to-multiple-lines-with-surround-vim"
   :1,3norm yss"

   # Git 插件
   Plugin 'tpope/vim-fugitive' # 在 vim 里执行 :Gblame 可以看到当前文件每行代码的提交人和日期，找人背锅或者咨询的神器

   # 直接在 vim 里 diff 文件，比如打开了两个文件
   :windo diffthis
   :diffoff!

   # 解决mac vim 中文输入法的问题
   # https://www.jianshu.com/p/4d81b7e32bff
   # https://zhuanlan.zhihu.com/p/23939198
   brew tap daipeihust/tap && brew install im-select # 终端下执行此命令安装 im-select
   # 然后 vim 配置加入这一行 (ABC 是默认输入法，直接输入 im-select 可以看到你的默认值)
   `autocmd InsertLeave * :silent !/usr/local/bin/im-select com.apple.keylayout.ABC`


   # 如果跳转到跳转之前的位置, https://vi.stackexchange.com/questions/2001/how-do-i-jump-to-the-location-of-my-last-edit
   # 使用场景：比如在当前函数里使用了logging，发现logging import，我会跳转到文件头去 import logging，编辑完后进入normal模式使用  `` 就可以跳转到之前编辑位置
   `` which will bring you back to where the cursor was before you made your last jump. See :help `` for more information.

   # 如何编辑远程服务器文件, https://superuser.com/questions/403664/how-can-i-copy-and-paste-text-out-of-a-remote-vim-to-a-local-vim
   :e scp://user@host/relative/path/from/home.txt

   # 跳转
   g<c-]> # list all match tag

   # 跳转到上一个 insert 的位置，经常用在修改之后跳转到之前的编辑位置 https://vi.stackexchange.com/questions/2001/how-do-i-jump-to-the-location-of-my-last-edit
   `^ 或者 '^

   # vim 替换不间断空格，illegal character U+00A0异常解决。https://www.jianshu.com/p/5f9992e5cd47
   :%s/\%u00a0/ /g

   # set transparent，设置透明，如果iterm2 设置了背景图可以看到
   :hi normal guibg=000000

   # vim 去掉 ^M 字符（这个字符用 type CTRL-V, then CTRL-M. 打出来）。
   # 或者 brew isntall dos2unix，然后 dos2unix filename
   :s/^M$//


   # vim 鼠标拖移窗口大小。设置鼠标支持即可。如果不生效：Iterm2->Profiles->Terminal->"Enable mouse reporting" 勾一下
   # 参考：https://stackoverflow.com/questions/62582721/how-to-fix-restore-mouse-controls-in-tmux-on-iterm2
   :set mouse=a

   # 如何全局替换多个文件的字符串。使用 far.vim 或者如果安装了 coc.nvim 可以使用 CocSearch 命令
   :Far foo bar **/*.py

   # iterm2 下 set mouse=a 之后vim/neovim不生效，无法鼠标拖动窗口大小
   Settings -> Profiles -> Terminal -> Enable mouse reporting


* `《vim cheet sheet》 <https://vim.rtorr.com/lang/zh_cn/>`_


vim-go/coc.nvim plugin Tips
-----------------------------------------

.. code-block:: vim

  # 最近一直在开发机服务器上直接用 neovim+vim-go+coc.nvim 写 golang，具有完备开发功能(vim-go借助各种go工具实现)
  # https://github.com/fatih/vim-go
  # https://github.com/fatih/vim-go-tutorial  # vim-go 官方教程，最好过一遍
  let g:go_def_mode='godef'  # 有时候 gopls 有问题可以用 godef 跳转，默认用 gopls

  # 如何生成 interface 接口定义
  type S struct{}   # cursor 放在 S 上执行 :GoImpl io.Reader

  # 跳转到接口的实现 https://github.com/fatih/vim-go/issues/820
  :GoDef (或ctrl+]) 跳转到定义，但是如果是接口实现只能跳转到 interface 定义而非 struct 实现。
  :GoCallees 从函数调用处跳转到接口的真正实现，而不是接口定义 (在方法调用点使用 -> struct 方法实现列表)
  :GoCallers 找到当前函数被调用的地点 (caller 主调， callee 被调)
  :GoImplements 获取一个接口方法的所有实现列表。(interface method -> implement method list)

  # 常用的方便命令(命令模式Tab补全), 参考 https://github.com/fatih/vim-go/blob/master/doc/vim-go.txt
  :GoFmt 格式化，你可以配置 vim-go 直接保存自动执行格式化或者直接执行 GoImports
  :GoRun, GoTest, GoTestFunc 运行代码和单测
  :GoMetaLinter 执行 lint，可以配置 .gometalinter.json 忽略一些 lint 错误。https://github.com/PegasusWang/linux_config/blob/master/golang/gometalinter.json
  :GoRename 快速重构
  :GoImpl 为 struct 生成接口函数定义(光标放到struct定义上使用)。如果一个 interface 有很多需要实现的函数，比较方便
  :GoAddTags GoRemoveTags json 快速给 struct field 增加 json tag，支持 visual 模式多选。默认 tag 名是下划线命名
  :GoKeyify 把无名称初始化的 struct literals 转成包含字段名的初始化方式
  :GoIfErr 生成 if err 返回值(或者用 snippets)
  :GoChannelPeers 寻找可能的 channel 发送和接收点
  :GoFillStruct 给一个 struct 填充默认值

  # 甚至还可以让超过 120 行的代码自动折行，需要安装 https://github.com/segmentio/golines
  # golines -w -m 120 red_dot.go  # 直接命令行格式化，gofmt 没有长行的折行功能
  # 在 vim 中使用 golines
  let g:go_fmt_command = "golines"
  let g:go_fmt_options = {
    \ 'golines': '-m 120',
    \ }


  " 以下是 coc.nvim 官方示例定义的快捷键。用好这几个快捷键可以给开发带来极大便利
  " 跳转到变量定义。normal 模式下在一个变量名上按一下 gd 即可跳转到定义位置，然后ctrl-o 可以快速返回原位置
  nmap <silent> gd <Plug>(coc-definition)
  " 跳转到值的类型定义，或者跳转到函数的返回值类型。在你想要快速查找一个类型的结构的时候非常有用
  nmap <silent> gy <Plug>(coc-type-definition)
  " 跳转到 interface 接口的对应实现。比如查看go里一个 interface 被哪些 struct 实现了。如果在 struct 名字上使用可以找到当前 struct 实现了哪些 interface
  nmap <silent> gi <Plug>(coc-implementation)
  " 打开当前变量、函数等被引用的列表。比如看一个 函数 在哪些地方使用了
  nmap <silent> gr <Plug>(coc-references)


用markdown文件制作html ppt
-------------------------------------------------------------

.. code-block:: python

   apt-add-repository ppa:brightbox/ruby-ng
   apt-get update
   apt-get install ruby2.2
   gem install slideshow
   slideshow install deck.js
   sudo  pip install https://github.com/joh/when-changed/archive/master.zip
   when-changed rest.md slideshow  build rest.md -t deck.js

   # mac: brew install fswatch, http://stackoverflow.com/questions/1515730/is-there-a-command-like-watch-or-inotifywait-on-the-mac
   jfswatch -o ~/path/to/watch | xargs -n1 ~/script/to/run/when/files/change.sh
   fswatch -o ./*.py  | xargs -n1  ./runtest.sh    # 比如写单元测试的时候修改后就让测试执行

   # 也可以使用下边的工具用 Jupyter 做 slideshow，最大的特点是直接在浏览器里敲代码交互演示
   # Reveal.js - Jupyter/IPython Slideshow Extension, also known as live_reveal
   # https://github.com/damianavila/RISE

   # 更推荐使用 reveal-md
   reveal-md slides.md -w


PPT 技巧
-------------------------------------------------------------

.. code-block:: shell

   # 如何粘贴代码到 PPT 里边: 转成 rtf。直接粘贴没有代码高亮，转成 rtf 格式就可以了
   # https://superuser.com/questions/85948/how-can-i-embed-programming-source-code-in-powerpoint-slide-and-keep-code-highli
   # pip install Pygments
   pygmentize -f rtf code.py | pbcopy
   # 粘贴到 ppt 之后需要选择 “保留源格式”，这样代码才有高亮


Benchmark
-------------------------------------------------------------

.. code-block:: shell

    sudo apt-get install apache2-utils
    ab -c 并发数量 -n 总数量 url


Ffmpeg && youbute-dl
-------------------------------------------------------------

.. code-block:: shell

   # brew install youtube-dl
   # https://askubuntu.com/questions/486297/how-to-select-video-quality-from-youtube-dl
   # http://www.cnblogs.com/faunjoe88/p/7810427.html
   # 下载视频，支持油管、b 站等
   youtube-dl -F "http://www.youtube.com/watch?v=P9pzm5b6FFY"
   youtube-dl -f 22 "http://www.youtube.com/watch?v=P9pzm5b6FFY"
   youtube-dl -f bestvideo+bestaudio "http://www.youtube.com/watch?v=P9pzm5b6FFY"

   # 目前youtube-dl 貌似不更新了，用 yt-dlp 代替
   python3 -m pip install --force-reinstall https://github.com/yt-dlp/yt-dlp/archive/master.tar.gz
   # 比如映射了俩命令下载 MP3 或者 mp4
   alias download_youtube_mp3='yt-dlp --cookies-from-browser chrome  -x --audio-format mp3'
   alias download_youtube='yt-dlp --cookies-from-browser chrome -f "bestvideo[ext=mp4]+bestaudio[ext=m4a]" '

   # 转换格式，比如 flv -> mp4 https://superuser.com/questions/624565/ffmpeg-convert-flv-to-mp4-without-losing-quality
   ffmpeg -i input.flv -codec copy output.mp4

   # 截取视频
   ffmpeg -i input.mp4 -ss 00:01:00 -to 00:02:00 -c copy output.mp4
   # https://gist.github.com/PegasusWang/11b9203ffa699cd8f07e29559cc4d055
   # 截图
   ffmpeg -ss 00:10:00 -i "Apache Sqoop Tutorial.mp4" -y -f image2 -vframes 1 test.png

   # 提取音频mp3, https://stackoverflow.com/questions/9913032/ffmpeg-to-extract-audio-from-video
   ffmpeg -i sample.avi -q:a 0 -map a sample.mp3

   # 连接视频
   $ cat input.txt
   file '/path/to/file1'
   file '/path/to/file2'
   file '/path/to/file3'
   # 注意用 -safe 0
   ffmpeg -f concat -safe 0 -i input.txt -c copy output.mp4

   # youtube-dl 下载音频: https://askubuntu.com/questions/178481/how-to-download-an-mp3-track-from-a-youtube-video
   youtube-dl --extract-audio --audio-format mp3 <video URL>
   # use socks5 proxy
   youtube-dl --proxy 'socks5://127.0.0.1:1080' [URL]

   # use aria2 # https://blog.51cto.com/14046599/2348642
   # brew install aria2
   youtube-dl https://www.youtube.com/watch?v=zAJUeZ0SNp8 --external-downloader aria2c --external-downloader-args "-x 16 -k 1M"

   # 音频 mp3 处理
   ffmpeg -i input.mp3 -ss 00:00:00 -t 00:03:00 -acodec copy output.mp3  # 截取mp3
   ffmpeg -i audio.wav -acodec libmp3lame audio.mp3 # wav to mp3


.. code-block:: python

   # 脚本下载 youtube 视频
   #!/usr/bin/env python
   # -*- coding:utf-8 -*-

   # pip install youtube_dl，如果报错尝试升级
   # pip install --upgrade youtube_dl

   """
   目前 youtube_dl 不再更新，可以用 yt-dlp 代替
   python3 -m pip install --force-reinstall https://github.com/yt-dlp/yt-dlp/archive/master.tar.gz
   import yt_dlp as youtube_dl
   """

   from __future__ import unicode_literals
   import youtube_dl


   class MyLogger(object):
       def debug(self, msg):
           pass

       def warning(self, msg):
           pass

       def error(self, msg):
           print(msg)


   def my_hook(d):
       if d['status'] == 'finished':
           print('Done downloading, now converting ...')


   ydl_opts = {
       'format': 'bestaudio/best',
       'postprocessors': [{
           'key': 'FFmpegExtractAudio',
           'preferredcodec': 'mp3',
           'preferredquality': '192',
       }],
       'logger': MyLogger(),
       'progress_hooks': [my_hook],
   }
   with youtube_dl.YoutubeDL(ydl_opts) as ydl:
       url = 'https://www.youtube.com/watch?v=48VSP-atSeI'
       ydl.download([url])

Vlog 如何增加字幕
-------------------------------------------------------------

- https://github.com/BingLingGroup/autosub/blob/dev/docs/README.zh-Hans.md#%E8%AF%AD%E9%9F%B3%E8%BD%AC%E6%96%87%E5%AD%97%E7%BF%BB%E8%AF%91api%E8%AF%B7%E6%B1%82
- https://www.zhihu.com/question/24717723/answer/290003526

.. code-block:: shell

   # 首先安装 autosub。先安装 brew install ffmpeg
   pip3 install git+https://github.com/BingLingGroup/autosub.git@alpha ffmpeg-normalize
   # 使用方式。最后生成 srt 文件，名字为 视频.zh-cn.rst
   autosub -S zh-cn -D zh-cn -i 视频.mp4
   # 之后可以使用软件比如 ArcTime 或者之类的软件可以导入并生成新的视频。
   # 使用 ffmpeg 也可以增加字幕并输出到新的 mp4
   ffmpeg -i 视频.mp4 -vf subtitles=视频.zh-cn.srt output.mp4

Curl
-------------------------------------------------------------

.. code-block:: shell

   # 记录 curl 过程, https://askubuntu.com/questions/944788/how-does-curl-print-to-terminal-while-piping
   # 注意，如果 url 地址里边有 & 符号记得 url 两边加上双引号
   curl -v http://httpbin.org/headers > t.txt 2>&1


* `《Linux工具快速教程》 <https://linuxtools-rst.readthedocs.io/zh_CN/latest/>`_
* `《slide show》 <http://slideshow-s9.github.io/>`_
* `《markdown sheet》 <http://commonmark.org/help/>`_
* `《CONQUERING THE COMMAND LINE》 <http://conqueringthecommandline.com/book/>`_

Pandoc 转换文档格式
-------------------------------------------------------------

.. code-block:: shell

  # https://pandoc.org/demos.html
  pandoc -s -o about.md about.rst
  # markdown 转成 word
  pandoc -o output.docx -f markdown -t docx filename.md
  # rst -> github markdown
  pandoc file.rst -f rst -t gfm -o filename.md
  # markdown -> epub
  pandoc xxx.md -o xxx.epub

Calibre 电子书管理工具
-------------------------------------------------------------

.. code-block:: shell

  # https://www.jianshu.com/p/0bcb92509309
  # https://snowdreams1006.github.io/myGitbook/advance/export.html
  # 通过 calibre 提供的二进制工具抓取并且生成电子书
  ebook-convert draveness.recipe draveness.mobi --output-profile kindle

  # 如何合并多个epub文件
  # https://www.mobileread.com/forums/showthread.php?t=121691
  # https://github.com/JimmXinu/EpubMerge/blob/main/epubmerge.py
  python3 epubmerge.py -o all.epub a.epub b.epub c.epub


Gitbook
-------------------------------------------------------------

.. code-block:: shell

  # gitbook 本地生成电子书 pdf（依赖 calibare 的 ebook-convert)
  npm install -g gitbook
  npm install -g gitbook-cli
  # 本地预览
  gitbook build; gitbook serve
  # 生成 pdf
  gitbook pdf
  # 如果 polyfills.js 报错了 https://www.cnblogs.com/cyxroot/p/13754475.html

Mac 微信
-------------------------------------------------------------

.. code-block:: shell

  # 微信小助手：https://github.com/MustangYM/WeChatExtension-ForMac
  # 支持微信多开、消息防撤回、微信皮肤等多种功能。懒人安装
  curl -o- -L https://omw.limingkai.cn/install.sh | bash -s

iphone telegram 新设备迁移
-------------------------------------------------------------

.. code-block:: shell

    1. 新的 ios 设备上用 chrome 登录网页版telegram，可以选择扫描二维码登录。
    2. 老的ios机器登录app->设置->设备->扫描二维码，此时新设备登录上网页版
    3. 新设备左上角网页版设置->隐私和安全->Passkeys 点击创建，此时可以创建一个新的 passkey，且新设备会保存这个passkey。
    4. 新设备重启一下app版本的telegram ，选择这个passkey登录即可


Wireshark(mac tcp 抓包)
-------------------------------------------------------------

Capture -> Options -> lo0 抓本地 127.0.0.1 包。筛选 tcp.port == 6379 抓 redis tcp 包
抓包后点击一条选择右键 Follow -> TCP Stream 就可以查看 tcp 包发送的文本内容。

抓包iOS: 输入 rvictl -s 设备[udid]。格式是rvictl -s [设备udid]，设备的udid可以通过itunes或者itools获取
``system_profiler SPUSBDataType | grep "Serial Number:.*" | sed s#".*Serial Number: "##``


- https://serverfault.com/questions/22990/is-there-a-way-to-get-wireshark-to-capture-packets-sent-from-to-localhost-on-win
- https://www.jianshu.com/p/62f00db7be68
- http://mrpeak.cn/blog/wireshark/  Wireshark抓包iOS入门教程

Niz/HHKB 静电容键盘。Karabiner 修改 mac 键位配置
-------------------------------------------------------------

HHKB 静电容:

- HHKB 开关我只打开了 2 （mac 模式），貌似网上有说打开开关 6 会出现无法唤醒的问题。
- Mac 模式 HHKB 可以用使用 Fn+Esc 休眠。
- 如何禁用内置键盘： Karabiner-Elements 可以在插入外置键盘的时候禁用内置键盘，配置在 Devices -> Advanced， 勾选 Disable the built-in keyboard.
- 网易云音乐切歌：使用 Fn + 7/8/9 分别是上一首，暂停和下一首

Niz Atom66 静电容:

- Niz 键盘(Atom66)可以用 Fn+Option+Command 切换mac和win模式。左下角ctrl 的右边三个键一起按闪灯两次即是 mac 模式
- Niz 键盘如果感觉不灵敏了比如按键按下去有时候没有出来字符，可能是误调整了键程。niz66 可以通过 fn+7 调节，亮灯一下键程最短最灵敏
- 如何锁定键盘？ niz atom66 可以使用 Fn+Esc 临时禁用键盘(再次按下恢复)，niz L84 在键盘 F1 位置上方边框有一个键盘锁按钮按下即可。

Niz L84 矮轴静电容:

- Niz L84 键盘，有时候会误触 command+f1 导致外置显示器屏幕镜像，可以重新按一下恢复。
- Niz L84 键盘，左空格失灵了。可以长按 Fn+左空格恢复。 "晓览外设" 可以关注公众号有niz宁芝键盘系列说明书。https://mp.weixin.qq.com/s/S8dpXg6h4WHEUFOPg4Kv5A
- Niz L84 键盘有线版除了自带的数据线，还可以直接用苹果的双头typec充电线使用
- 如果键盘失灵了，尝试 Esc+左Ctrl+Delete+方向右键 (四角的四个键) 5 秒后重置。
- L84初始化：``Fn+左Alt(左下角第三个) 同时按住三秒切到mac模式(状态等闪2次); Fn+\同时按住 3 秒切换 \ 和BackSpace``

如何使用 mac 使用 Karabiner-Elements  改键配置

- https://github.com/tekezo/Karabiner-Elements
- https://www.jianshu.com/p/47d5de7f12bc
- https://madogiwa.github.io/KE-complex_modifications/
- https://zhuanlan.zhihu.com/p/391173661 niz66说明书

配置文件放置位置在 https://github.com/PegasusWang/linux_config/blob/master/mac_karabiner/wasd.json

~/.config/karabiner/assets/complex_modifications/wasd.json

这里我把 right_command + WASD 修改成上下左右，方便 HHKB 方向键移动，默认的 HHKB 方向键不方便。
目前键盘已经从 HHKB 切换到 niz 静电容 35 克，长期打字对小指头挺友好的，再也没疼过。niz支持切换键程，个人一般习惯切到最短的
键程打字比较顺畅，轻点一下按键就可以触发。


Goland IDE
-------------------------------------------------------------
代码可以正常运行，但是打开项目飘红:

- 可能是缓存已满。File->Invalidate Caches -> Invalidate and Restart
- 项目设置问题。设置->Go -> Go modules -> 勾选 Enable Go modules integration
- Mac M1 芯片 IDEA GoLand 卡顿解决方式。 Help -> 选择 Edit Custom VM Options 然后粘贴以下代码：

.. code-block:: shell

    -XX:+UseNUMA
    -Xms2048m
    -Xmn2048m
    -XX:MaxMetaspaceSize=2048m
    -XX:ReservedCodeCacheSize=2048m
    -Xmx8192m
    -Dfile.encoding=UTF-8
    -XX:SoftRefLRUPolicyMSPerMB=500
    -XX:CICompilerCount=2
    -XX:+HeapDumpOnOutOfMemoryError
    -XX:-OmitStackTraceInFastThrow
    -ea
    -Dsum.io.useCanonCaches=false
    -Djdk.http.auth.tunneling.disabledSchemes=""
    -Djdk.attach.allowAttachSelf=true
    -Djdk.module.illegalAccess.silent=true
    -Dkotlinx.coroutines.debug=off
    -XX:ErrorFile=$USER_HOME/java_error_in_idea_%p.log
    -XX:HeapDumpPath=$USER_HOME/java_error_in_idea.hprof
    -XX:+IgnoreUnrecognizedVMOptions
    -Dide.no.platform.update=true
