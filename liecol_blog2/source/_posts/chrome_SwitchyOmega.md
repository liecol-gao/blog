---
title: chrome通过SwitchyOmega配置代理
date: 2017-01-11 17:56:30
tags: [chrome,Google]
categories:
- linux
---

之前公司搭建的vpn丢包80%以上，搞的我很郁闷，玩不了。
然后就找了一种新的PAC脚本方式，在这做个记录。

* 安装 SwitchyOmega.

这个是google的一个扩展程序。
SwitchyOmega可以在谷歌应用商店安装。

如果没法连接，也可以在[github下载安装最新版](https://github.com/FelisCatus/SwitchyOmega/releases)安装。
安装直接拖动到 google->更多工具->扩展程序即可，上面的链接也有显示如何安装。
<!--more-->

{% img "" /images/chrom_extensions.png 700 280 liecol-晓斌|谷歌扩展图 liecol-晓斌|谷歌扩展图 %}

* 新建情景模式
* 选择PAC情景模式
{% img "" /images/add_extension_switch.png 700 280 liecol-晓斌|新建情景模式 liecol-晓斌|新建情景模式 %}

* PAC网址选为
https://raw.githubusercontent.com/kgfw/fg/master/pac/cfu.pac

{% img "" /images/pac_address.png 700 280 liecol-晓斌|PAC地址 liecol-晓斌|PAC地址 %}

* auto switch 设置
有些网站可以不用走代理，就可以在此设置。

{% img "" /images/auto_switch.png 700 280 liecol-晓斌|auto switch liecol-晓斌|auto switch %}


然后google选择对应的模式即可访问了。
