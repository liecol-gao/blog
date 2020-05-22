---
title: windows/python/virtualenv环境安装pyramid
date: 2016-07-14 17:46:29
tags:
---

关于win-py-vir的环境安装技巧在之前就有记录(详情参考windows＋Python＋virtualenv环境配置)

这里主要讲在虚拟环境下如何安装python框架pyramid

pyramid采用mako模板引擎,SQLAlchemy ORM工具，可配置多样数据库,如sqlite，mysql等(如若有时间，以后也会整理出一份文档)

在安装python库pip或者easy_install后

采用
```
pip install pyramid ==1.4或者

easy_install pyramid == 1.4
```
后面的1.4是版本，如若不加则采用最新版

但是pyramid不支持高版本对低版本的兼容，所以谨慎处理

linux采用yum或者apt-get处理，这里不做详细介绍

安装之后，需要进入虚拟环境
```
Scripts\activate
```
执行在路径Script下activate

开启环境后可建立项目(建议项目和环境文件夹分开，因为如若环境版本不可控，可删除从新载入项目)

创建项目
````
pcreate -s starter myproject(项目名称)

pcreate -s alchemy C:\simpleCRUD（项目名称）
```
如果要分离的话前面加上路径即可。

在创建项目后，还需要增加项目的内置包
```
python setup.py develop
```
然后进入项目启动项目
```
pserve development.ini
```
目录安装完成，大体说一下文件作用，但不尽详细。

这里我们首先看一下Myroject这个项目根目录下的几个文件：

1. CHANGES.txt

    本项目的变更文件，推荐采用ReST格式编写

2. RADME.txt

    项目描述文件，推荐采用ReST格式编写

3. development.ini

    开发时参数配置文件

4. production.ini

    运行时参数配置文件

5. setup.cfg

    这是setup.py使用的配置文件

6. MANIFEST.in

    打包清单，列出了打包时需要一起打到python程序包中的文件

7. setup.py

    标准的setuptools的setup.py文件，用于测试、分发该项目

在MyProject项目目录中，还有一个叫myproject的包目录，这个目录下包含了如下内容

1. __init__.py

    初始化文件。包含了一些启动本项目的指令。

2. templates

    存放开发模板文件的目录

3. tests.py

    存放单元测试

4. views.py

    存放可调用视图

各版本不一致，但主体文件差不多都是这样，也可以自行增加view文件夹开发。

create By  
liecol-晓斌