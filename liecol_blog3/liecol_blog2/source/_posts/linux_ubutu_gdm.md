---
title: 在vmware上安装ubuntu16.04无法进入桌面或者桌面空白解决办法
date: 2017-09-21 11:27:00
tags: [linux,Ubuntu]
categories:
- linux
---

在虚拟机上安装ubuntu出现了这样的情况，开机后不能进入图形桌面，只能按crl+alt+f1~f6，在文本界面下登陆。
** 解决办法 **：
step1:
按`crl+alt+f1`写换到文本模式下，键入用户名和密码登陆，然后切换到超级用户状态下:

step2：
输入`startx`命令切换到图形界面，会发图形界面什么都没有。

step3：
按`crl+alt+t`进入超级终端，安装`gdm`

step4: 
```
sudo apt-get install gdm 
# 登录显示器

sudo apt-get install ubuntu-desktop
# ubuntu桌面

sudo apt-get install gnome-core 
# 图形桌面环境
```
step5: 
重启ubuntu。

