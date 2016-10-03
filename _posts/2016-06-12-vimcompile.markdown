---
layout:     post
title:      "Vim 的编译安装与卸载"
subtitle:   "Compile Vim"
description: "Vim 编译 安装 卸载"
date:       2016-06-12 01:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- Linux
- 软件使用
- 编程开发

---

### 一、安装编译所需的工具和库
~~~
sudo apt install libncurses5-dev libgnome2-dev libgnomeui-dev libgtk2.0-dev libatk1.0-dev libbonoboui2-dev libcairo2-dev libx11-dev libxpm-dev libxt-dev python-dev python3-dev ruby-dev mercurial
~~~

### 二、下载最新版Vim
[下载链接](https://github.com/vim/vim/releases)

### 三、编译安装Vim
解压下载的源码，并切换到当前目录，可通过以下命令查看支持的编译选项：  

~~~
./configure --help
~~~
我用的编译选项：  

~~~
./configure --with-features=huge --enable-rubyinterp=yes --enable-luainterp=yes --enable-perlinterp=yes --enable-tclinterp=yes --enable-pythoninterp=yes --with-python-config-dir=/usr/lib/python2.7/config-x86_64-linux-gnu/ --enable-python3interp=yes --with-python3-config-dir=usr/lib/python3.5/config-3.5m-x86_64-linux-gnu --enable-gui=auto --enable-cscope --enable-xim --enable-multibyte --prefix=/usr
~~~
安装：  

~~~
sudo make VIMRUNTIMEDIR=/usr/share/vim/vim80
sudo make install
~~~
查看可使用的功能：  

~~~
vim --version
~~~

### 四、卸载
~~~
sudo make uninstall
~~~



