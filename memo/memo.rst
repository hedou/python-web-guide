.. _memo:

个人备忘录
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


Pip
---------------------------------------------------------------
.. code-block:: python

   # https://stackoverflow.com/questions/12332975/installing-python-module-within-code
   # pip install
   import pip

   def install(package):
       pip.main(['install', package])

   # Example
   if __name__ == '__main__':
       install('argh')


更换源, vi ~/.pip/pip.conf

.. code-block:: shell

   [global]
   timeout = 60
   index-url = http://pypi.douban.com/simple
   trusted-host = pypi.douban.com


IPython
---------------------------------------------------------------

.. code-block:: sh

   # ipython 如何使用 autoreload，每次重新修改了文件都得重新重启 ipython 很麻烦，解决方式
   # https://support.enthought.com/hc/en-us/articles/204469240-Jupyter-IPython-After-editing-a-module-changes-are-not-effective-without-kernel-restart
   # https://stackoverflow.com/questions/1254370/reimport-a-module-in-python-while-interactive
   # http://ipython.readthedocs.io/en/stable/config/extensions/autoreload.html
   In [1]: %load_ext autoreload
   In [2]: %autoreload 2


.. code-block:: python

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
.. code-block:: python

   # ~/.pdbrc
   # https://github.com/gotcha/ipdb/issues/111

   import os
   alias kk os._exit(0)    # 如果不幸在循环里打了断点，可以用 os._exit(0) 跳出

   alias pd for k in sorted(%1.keys()): print "%s: %s" % (k, (%1[k]))

   # https://stackoverflow.com/questions/21123473/how-do-i-manipulate-a-variable-whose-name-conflicts-with-pdb-commands
   # 如果 pdb 里的内置命令和内置函数冲突了，可以加上 ! 使用内置函数
   !next(iter)

Mac
---------------------------------------------------------------
.. code-block:: python

   # 文件字符串批量替换，git项目里替换的时候注意指定文件类型，防止破坏git信息
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

   # XXX.APP已损坏,打不开.你应该将它移到废纸篓 MACOS 10.12 SIERRA
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
   brew install rmtrash

   # 增加 terminal 光标移动速度, https://stackoverflow.com/questions/4489885/how-can-i-increase-the-cursor-speed-in-terminal
   #defaults write NSGlobalDomain KeyRepeat -int 1
   mac: 系统设置-> 键盘 -> 修改按键重复到最快，重复前延迟最短。可以让光标在终端里移动更快

   # 如何在文件更新之后自动刷新浏览器，需要首先 pip 安装 when-changed
   alias flush_watch_refresh_chrome=" when-changed -v -r -1 -s ./ osascript -e 'tell application \"Google Chrome\" to tell the active tab of its first window to reload' "


如何发送 mac 通知，可以用来做提示

.. code-block:: python

   # https://stackoverflow.com/questions/17651017/python-post-osx-notification
   # 配合 crontab 可以用来做一个简单的定时任务提醒功能 57-59 17 * * * python ~/.tmp/noti.py


   # ~/.tmp/noti.py
   import os

   def notify(title, text):
       os.system("""
                 osascript -e 'display notification "{}" with title "{}"'
                 """.format(text, title))

   notify("开会啦", "Go Go Go !!!")


Mac 蓝牙耳机
---------------------------------------------------------------

如何给Macbook Pro 开启 Apt-X 蓝牙音质 https://www.jianshu.com/p/a1efa561ed9e
使用播放器 Audirvana Plus


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



Ubuntu相关
---------------------------------------------------------------

.. code-block:: python

    # 查看版本
    lsb_release -a

    # virtual box虚拟机和windows主机共享目录方法：安装增强工具；win主机设置共享目录例如ubuntu_share；在ubuntu里建立/mnt/share后使用命令：

    sudo mount -t vboxsf ubuntu_share /mnt/share/

    # 映射capslock 为　ctrl
    setxkbmap -layout us -option ctrl:nocaps

    # 文件字符串批量替换
    grep oldString -rl /path | xargs sed -i "s/oldString/newString/g"

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

    # scp
    scp someuser@192.168.199.1:/home/someuser/file ./    # 远程机器拷贝到本机
    scp ./file someuser@192.168.199.1:/home/someuser/    # 拷贝到远程机器

    # tar
    tar zxvf FileName.tar.gz    # 解压
    tar zcvf FileName.tar.gz DirName    # 压缩

代码搜索用ag, 比ack快

.. code-block:: python

    sudo apt-get install silversearcher-ag    # ubuntu
    brew install ag
    ag string dir/    # search dir
    ag readme$    # regular expression
    ag -Q .rb    # Literal Expression Searches, search for the exact pattern
    ag string -l    # Listing Files (-l)
    ag string -i    # Case Insensitive Searches (-i)
    ag string -G py$    # 搜索应py结尾的文件
    ag readme -l --ignore-dir=railties/lib    # 忽略文件夹
    ag readme -l --ignore-dir="*.rb"    # 忽略特性类型文件
    .agignore    # 用来忽略一些vcs，git等文件。


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


可以用如下方式执行依赖其他模块的python脚本，用run.sh执行run.py，记得chmod +x可执行权限，运行前执行下sh脚本测试能否成功

.. code-block:: python

    #!/usr/bin/env bash
    PREFIX=$(cd "$(dirname "$0")"; pwd)
    cd $PREFIX
    source ~/.bashrc

    python -u run.py    # -u 参数强制刷新输出
    date


对于python脚本，给main函数加上装饰器@single_process可以保证只有一个该脚本会执行, pip install single_process，比如下面这个run.py

.. code-block:: shell

    #!/usr/bin/env python
    # -*- coding:utf-8 -*-

    import time
    from single_process import single_process    # pip install single_process


    @single_process    # 保证不会同时执行，原理请看single_process源码。新版本貌似改了用法，非装饰器
    def main():
        time.sleep(10)
        print(time.time())

    if __name__ == '__main__':
        main()


* `《crontab快速参考》 <http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html>`_


Iterm2
-------------------------------------------------------------

.. code-block:: sh

   # https://stackoverflow.com/questions/11913990/iterm2-keyboard-shortcut-for-moving-tabs-around
   # Preferences/Keys 自定义配置使用 Cmd +jk 来在 Iterm2 tab 前后移动，模仿 vim 键位


Tmux
-------------------------------------------------------------

.. code-block:: sh

   tmux rename -t oriname newname
   tmux att -t name -d               # -d 不同窗口全屏
   # 如果手贱在本机tmux里又ssh到服务器又进入服务器的tmux怎么办
   c-b c-b d

   # Vim style pane selection
   bind -n C-h select-pane -L
   bind -n C-j select-pane -D
   bind -n C-k select-pane -U
   bind -n C-l select-pane -R

   # https://stackoverflow.com/questions/22138211/how-do-i-disconnect-all-other-users-in-tmux
   tmux a -dt <session-name>

   # 如何 ssh 后自动 attach 到某个 session
   if [[ "$TMUX" == "" ]] && [[ "$SSH_CONNECTION" != "" ]]; then
       # Attempt to discover a detached session and attach it, else create a new session
       WHOAMI="lens"   # attach 的 session 名称
       if tmux has-session -t $WHOAMI 2>/dev/null; then
           tmux -2 attach-session -t $WHOAMI
       else
           tmux -2 new-session -s $WHOAMI
       fi
   fi


SSH
-------------------------------------------------------------

.. code-block:: python

   # https://superuser.com/questions/98562/way-to-avoid-ssh-connection-timeout-freezing-of-gnome-terminal/98565#98565
   Press Enter, ~, . one after the other to disconnect from a frozen session.

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
    git diff commit1 commit2

    # 冲突以后使用远端的版本： NOTE：注意在 git merge 和 git rebase 中 ours/theirs 含义相反
    # rebase 场景下，theirs 实际表示的是当前分之
    # merge 场景下相反，theirs 表示的确是远端分之
    # https://stackoverflow.com/questions/16825849/choose-git-merge-strategy-for-specific-files-ours-mine-theirs
    git checkout --theirs templates/efmp/campaign.mako

    # 防止http协议每次都要输入密码：
    git config --global credential.helper 'cache --timeout=36000000'      #秒数

    # 暂存和恢复，当我们需要切分支又暂时不想 git add，可以先把目前的修改咱存起来
    git stash
    git stash apply
    git stash apply stash@{1}
    git stash pop # 重新应用储藏并且从堆栈中移走
    # 显示 git stash 内容 https://stackoverflow.com/questions/7677736/git-diff-against-a-stash
    git stash show -p  # see the most recent stash
    git stash show -p stash@{1}

    # 删除远程分之
    git push origin --delete {the_remote_branch}

    # 手残 add 完以后输入错了 commit 信息
    git commit --amend

    # 撤销 add （暂存），此时还没有 commit。比如 add 了不该 add 的文件
    git reset -- file
    git reset # 撤销所有的 add

    # 撤销修改
    git checkout -- file

    # 手残pull错了分支就(pull是先fetch然后merge)。或者 revert 一个失误的 merge
    git reset --hard HEAD~

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

    # 查看add后的diff
    git diff --staged

    # http://weizhifeng.net/git-rebase.html
    # rebase改变历史, 永远不要用在master分之，别人有可能使用你的分之时也不要用
    # only change history for commits that have not yet been pushed
    # master has changed since I stared my feature branch, and I want bo bring my branch up to date with master. - Dont't merge. rebase
    # rebase: finds the merge base; cherry-picks all commits; reassigns the branch pointer.
    # then git push -f
    # git rebase --abort

    # 全局 ignore, 对于不同编辑器协作的人比较有用，或者用来单独忽略一些自己建立的测试文件等
    git config --global core.excludesfile ~/.gitignore_global

    # 拉取别人远程分支，在 .git/config 里配置好
    git fetch somebody somebranch
    git checkout -b somebranch origin/somebranch

    # prune all the dead branches from all the remotes
    # https://stackoverflow.com/questions/17933401/how-do-i-remove-deleted-branch-names-from-autocomplete?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa
    git fetch --prune --all

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


vim
----

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

   # 解决中文输入法的问题
   # https://www.jianshu.com/p/4d81b7e32bff
   # https://zhuanlan.zhihu.com/p/23939198

   # 如果跳转到跳转之前的位置, https://vi.stackexchange.com/questions/2001/how-do-i-jump-to-the-location-of-my-last-edit
   # 使用场景：比如在当前函数里使用了logging，发现logging import，我会跳转到文件头去 import logging，编辑完后进入normal模式使用  `` 就可以跳转到之前编辑位置
   `` which will bring you back to where the cursor was before you made your last jump. See :help `` for more information.

   # 如何编辑远程服务器文件, https://superuser.com/questions/403664/how-can-i-copy-and-paste-text-out-of-a-remote-vim-to-a-local-vim
   :e scp://user@host/relative/path/from/home.txt


* `《vim cheet sheet》 <https://vim.rtorr.com/lang/zh_cn/>`_

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


PPT
-------------------------------------------------------------


.. code-block:: shell

   # 如何粘贴代码到 PPT 里边，转成 rtf
   # https://superuser.com/questions/85948/how-can-i-embed-programming-source-code-in-powerpoint-slide-and-keep-code-highli
   # pip install Pygments
   pygmentize -f rtf code.py | pbcopy


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
   youtube-dl -F "http://www.youtube.com/watch?v=P9pzm5b6FFY"
   youtube-dl -f 22 "http://www.youtube.com/watch?v=P9pzm5b6FFY"
   youtube-dl -f bestvideo+bestaudio "http://www.youtube.com/watch?v=P9pzm5b6FFY"

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


.. code-block:: python

   # 脚本下载 youtube 视频
   #!/usr/bin/env python
   # -*- coding:utf-8 -*-

   # pip install youtube_dl，如果报错尝试升级
   # pip install --upgrade youtube_dl
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

Curl
-------------------------------------------------------------

.. code-block:: shell

   # 记录 curl 过程, https://askubuntu.com/questions/944788/how-does-curl-print-to-terminal-while-piping
   curl -v http://httpbin.org/headers > t.txt 2>&1


* `《Linux工具快速教程》 <https://linuxtools-rst.readthedocs.io/zh_CN/latest/>`_
* `《slide show》 <http://slideshow-s9.github.io/>`_
* `《markdown sheet》 <http://commonmark.org/help/>`_
* `《CONQUERING THE COMMAND LINE》 <http://conqueringthecommandline.com/book/>`_


HHKB
-------------------------------------------------------------

Mac 使用 Fn+Esc 休眠。
mac 使用 Karabiner-Elements  改键配置

- https://github.com/tekezo/Karabiner-Elements
- https://www.jianshu.com/p/47d5de7f12bc

配置文件放置位置在 https://github.com/PegasusWang/linux_config/blob/master/mac_karabiner/wasd.json

~/.config/karabiner/assets/complex_modifications/wasd.json

这里我把 right_command + WASD 修改成上下左右，方便 HHKB 方向键移动，默认的 HHKB 方向键不方便。
