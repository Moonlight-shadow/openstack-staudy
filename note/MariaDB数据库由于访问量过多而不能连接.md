---
layout: post
title: "MariaDB数据库由于访问量过多而不能连接"
description: "MariaDB数据库由于访问量过多而不能连接"
category: openstack
tags: [openstack]
---
{% include JB/setup %}

##1.错误提示：
```bash
too many connnections  
```
##2.错误原因：
数据库系统允许的最大可连接数max_connections默认是100。最大是16384，如果没设置则是默认的100连接数，超过100数据库会因访问量过多而出现崩溃，不稳定等现象,在下面max_connections的值可设置位1~16384中的任意值，因事而异。
##3.解决方案：
编辑mysql（mariadb）配置文档：（ubuntu的是/etc/mysql/my.cnf ）编辑此文档的[mysqld]节，添加以下的语句
```bash
max_connections=1000  
```