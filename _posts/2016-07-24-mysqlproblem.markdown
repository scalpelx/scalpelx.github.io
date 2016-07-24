---
layout:     post
title:      "关于MySQL在日常使用中遇到的一些问题"
subtitle:   ""
description: "MySQL 问题 无法插入 中文 乱码 Error Code:1093 "
date:       2016-07-24 01:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- 数据库
- Linux
- 软件使用

---

### 问题一
由于MySQL默认字符编码为Latin，不支持中文，所以会遇到无法插入中文数据或者中文乱码的情况，这时我们可以更改其默认字符编码：打开`/etc/mysql/my.cnf`，添加

```
[mysqld]
character-set-server=utf8
```

### 问题二
在做课程设计的时候，发现无法执行下述语句：

```
update 账单
set 耗水量 = 当前水表数 - (select 当前水表数 from 账单 where 日期 = (select date_add('2016-01-01', interval -1 month)))
where 日期 = '2016-01-01'
```
错误信息：`Error Code: 1093. You can't specify target table '账单' for update in FROM clause`  
经过查阅资料得知，原来MySQL不能在同一语句中先select出同一表中的某些值，再update这个表（SQL Server可以），可以搞个中间表，设个别名过渡一下：

```
update 账单 
set 耗水量 = 当前水表数 - (select 当前水表数 from (select tmp.* from 账单 tmp)a  where a.日期 = (select date_add('2016-01-01', interval -1 month))) 
where 日期 = '2016-01-01';
```

### 问题三：
在删除数据库中的一条记录的时候，一直不能删除，提示错误信息：`Error Code: 1175. You are using safe update mode and you tried to update a table without a WHERE that uses a KEY column To disable safe mode, toggle the option in Preferences -> SQL Editor -> Query Editor and reconnect.`  
后来通过搜索资料，发现mysql有个叫SQL_SAFE_UPDATES的变量，SQL_SAFE_UPDATES有两个取值0和1，SQL_SAFE_UPDATES = 1时，不带where和limit条件的update和delete操作语句是无法执行的，即使是有where和limit条件但不带key column的update和delete也不能执行，为了数据库更新操作的安全性，此值默认为1，所以才会出现更新失败的情况。所以可以先执行`SQL_SAFE_UPDATES = 0;`或者在Workbench -> Edit -> Preferences -> SQL Editor中取消勾选Safe Updates。
