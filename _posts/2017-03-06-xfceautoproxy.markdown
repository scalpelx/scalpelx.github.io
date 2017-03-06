---
layout:     post
title:      "Xfce桌面环境下通过pac实现自动代理"
subtitle:   "Ubuntu + Shadowsocks"
description: "Ubuntu Xfce Shadowsocks pac gfwlist 代理 proxy "
date:       2017-03-06 01:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- Linux
---
### 安装准备
**安装[Shadowsocks-Qt5](https://github.com/shadowsocks/shadowsocks-qt5)**  
```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt update
sudo apt install shadowsocks-qt5
```
**安装pip**
```
sudo apt install python-pip
pip install --upgrade pip
```
**安装[GenPAC](https://github.com/JinnLynn/genpac)**
```
sudo pip install genpac
pip install --upgrade genpac
```
**下载[gfwlist](https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt)**
```
genpac -p "SOCKS5 127.0.0.1:1080" --gfwlist-proxy="SOCKS5 127.0.0.1:1080" --output="autoproxy.pac"
```
### 设置全局代理
在**/etc/environment**文件里添加**auto_proxy/AUTO_PROXY**变量即可
```
auto_proxy="file://~/autoproxy.pac"
AUTO_PROXY="file://~/autoproxy.pac"
```
重启
### 终端代理
终端无法通过此种方法走代理，可以安装[proxychains](https://github.com/haad/proxychains)  
```
sudo apt install proxychains
```
**修改配置**
编辑/etc/proxychains.conf文件，将socks4 127.0.0.1 9095（tor代理）修改为socks5 127.0.0.1 1080（shadowsocks代理）
**使用**
$ proxychains yourcommand  
例如proxychains wget https://www.google.com -v -O /dev/null
