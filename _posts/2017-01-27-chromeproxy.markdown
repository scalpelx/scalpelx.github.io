---
layout:     post
title:      "Ubuntu系统中Chrome使用系统代理的方法"
subtitle:   ""
description: "Ubuntu Chrome 代理 proxy "
date:       2017-01-27 01:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- Linux
---

　　最近发现Flash插件老是无法更新，后来查阅得知更新需要连接Google服务器，但我翻墙用的是SwitchyOmega+Shadowsocks，更新不走这个，可以通过添加启动参数的方法来指定Chrome的代理，终端命令如下  
　　`google-chrome --proxy-server="socks5://127.0.0.1:1080"`
