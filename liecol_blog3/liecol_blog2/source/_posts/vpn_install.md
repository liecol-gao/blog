---
title: 关于vpn的搭建
date: 2016-06-12 14:22:00
tags: [linux,vpn]
categories:
- linux
---

前提是得有一台可供用的服务器，不然搭建了和没搭建一样。 1:安装pptp
```
sudo apt-get install pptpd
```
2:配置ip
```
vi /etc/pptpd.conf
```
<!--more-->
打开或者配置以下内容： option /etc/ppp/pptpd-options localip 192.168.0.1(pptp服务器的ip地址设置为192.168.0.1) remoteip 192.168.0.1-255(pptp客户端的ip地址设置为192.168.0.1到192.168.0.255之间，这个可以根据自己需要的情况来设定)

3:配置客户端 DNS
```
vi /etc/ppp/pptpd-options
```
比如选择谷歌的开放ip ms-dns 8.8.8.8 ms-dns 8.8.4.4

4:添加用户
```
vi /etc/ppp/chap-secrets
client    server  secret          IP addresses
liecol    pptpd   liecol            *
```

用户名 (没有字符数限制) 服务器 (默认pptpd，如果更改，需要修改options.pptpd文件) 密码(上面pptpd文件的name行的内容用双引号的话，密码就不能包含双引号) ip(* 表示有pptpd随机分配ip,也可自行设定)

5:设置ip转发 打开IPv4转发,并重新载入
```
sudo sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/g' /etc/sysctl.conf
sudo sysctl -p
```
sysctl配置与显示在/proc/sys目录中的内核参数．可以用sysctl来设置或重新设置联网功能 -p 从指定的文件加载系统参数，如不指定即从/etc/sysctl.conf中加载

6:为 pptp连接设置nat，不然不能访问其他网站。
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```
也可以直接编辑 /etc/rc.local，在 exit 0 之前添加以上内容。

7:重启pptp
```
sudo service pptpd restart
```

8:通过手机或者电脑连接测试是否等连上vpn。

create By 
liecol-晓斌 
2016-06-12
