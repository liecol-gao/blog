---
title: Apache2.4与2.2版本访问配置对比
date: 2016-12-25 19:02:25
tags: [linux,Apache]
categories:
- linux
---

之前折腾服务器，从`Apache2.2`->`Nginx1.10`->`Apache2.4`
在指定挂载目录的时候出现这么一个问题,log显示为：
403错误
**You don't have permission to access / on this server.**
<!--more-->
当时想着Directory更改为如下方式：
```Apache
<Directory />
Options FollowSymLinks
AllowOverride None
Order deny,allow
Deny from all
</Directory>
```
发现依然不好使，后来发现2.2版本与2.4版本配置是不一致的。
```Apache
<Directory />
    Options Indexes FollowSymLinks
    AllowOverride None 
    Require all granted
</Directory>
```

Apache2.2 的配置:
```Apache
Order deny,allow\ #排序，先拒绝后允许
Deny from all #拒绝所有
```
```Apache
Order allow,deny #排序，先允许后拒绝
Allow from all #允许所有
```

Apache2.4 的配置:
```Apache
Require all denied #拒绝所有
```
```Apache
Require all granted #允许所有
```

根据2.4版本修改后，Apache成功启动。

Ps:后来发现又报404错误，发现在程序中写的.htacess未生效：
便再次更改:
```Apache
AllowOverride All    //若不设置为All，则所有的.htacess文件的规则都不生效
```
之后站点就成功跑通了。




