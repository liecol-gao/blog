---
title: locale修改linux语言环境
date: 2017-10-09 16:39:15
tags: linux
categories:
- linux
---

最近用python脚本做文件写入，进vim看是乱码，以为是自己编码没处理好，处理后，发现打开还是不对。
然后用tail，cat发现又没毛病，而且vim打开所有文件写中文都是如此。
用locale查看了一下当前的语言环境。
```
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
LANG=zh_CN.UTF-8
LANGUAGE=zh_CN:zh
LC_CTYPE="zh_CN.UTF-8"
LC_NUMERIC=zh_CN
LC_TIME=zh_CN
LC_COLLATE="zh_CN.UTF-8"
LC_MONETARY=zh_CN
LC_MESSAGES="zh_CN.UTF-8"
LC_PAPER=zh_CN
LC_NAME=zh_CN
LC_ADDRESS=zh_CN
LC_TELEPHONE=zh_CN
LC_MEASUREMENT=zh_CN
LC_IDENTIFICATION=zh_CN
LC_ALL=
```
### 出现如下错误
  * locale: Cannot set LC_CTYPE to default locale: No such file or directory
  * locale: Cannot set LC_MESSAGES to default locale: No such file or directory
  * locale: Cannot set LC_ALL to default locale: No such file or directory
<!--more-->

再查看一下，系统安装的locale
```
locale -a
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_COLLATE to default locale: No such file or directory
C
C.UTF-8
POSIX
en_US.utf8
zh_CN
zh_CN.gb2312
```
果然，没有对应的zh_CN.UTF-8

安装zh_CN.UTF-8
```
sudo locale-gen zh_CN.UTF-8
```
locale再次查看
```
LANG=zh_CN.UTF-8
LANGUAGE=zh_CN:zh
LC_CTYPE="zh_CN.UTF-8"
LC_NUMERIC=zh_CN
LC_TIME=zh_CN
LC_COLLATE="zh_CN.UTF-8"
LC_MONETARY=zh_CN
LC_MESSAGES="zh_CN.UTF-8"
LC_PAPER=zh_CN
LC_NAME=zh_CN
LC_ADDRESS=zh_CN
LC_TELEPHONE=zh_CN
LC_MEASUREMENT=zh_CN
LC_IDENTIFICATION=zh_CN
LC_ALL=
```
最后vim进脚本一看，不再继续乱码。
