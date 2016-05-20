---
layout:     post
title:      "Ubuntu禁止MySQL自动启动"
subtitle:   "Stop MySQL autorun on Ubuntu"
description: "Ubuntu 禁止 MySQL 自动启动 stop run"
date:       2016-05-20 01:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- Linux
- 软件使用

---
之前在网上搜到了好多方法，比如注释掉`/etc/init/mysql.conf`的`start on `，或者直接执行`sudo echo "manual" >> /etc/init/mysql.override`，以及`sudo update-rc.d -f mysql remove`都不管用，好像对于15.04之后的版本都失效了。后来终于搜到了一个[解决方法](http://askubuntu.com/a/656474)：`sudo systemctl disable mysql`
手动启动SQL服务：`service mysql start`
取消禁用：`sudo systemctl enable mysql` `sudo systemctl start mysql`
