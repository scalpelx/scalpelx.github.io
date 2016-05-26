---
layout:     post
title:      "解决Ubuntu Gnome 16.04中Fcitx不能在一些程序中正常使用的问题"
subtitle:   "Solve the problem about fcitx can't use in some application in ubuntu gnome 16.04"
description: "Ubuntu gnome 16.04 3.16 fictx"
date:       2016-05-26 01:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- Linux
- 软件使用

---
系统升级到16.04后发现在Wine QQ中无法输入中文，运行`fcitx-diagnose`，提示环境变量 XMODIFIERS的值被设为了"@im=ibus"而不是"@im=fcitx"等问题，可是在~/.xprofile以及xinitrc文件中添加相关设置都不管用，后来终于被我搜到了一篇[解决方案](https://blog.lutty.me/linux/gnome/2016-01/gnome-3-16-no-qt_im_module-xmodifiers.html)
:是因为ibus-daemon这个文件存在，所以系统才强制设置这两个环境变量为ibus的，只需要暂时搞掉这个文件，fcitx就正常了，可以输入中文了，执行以下命令即可：  
`sudo mv /usr/bin/ibus-daemon /usr/bin/ibus-daemon.bak`
