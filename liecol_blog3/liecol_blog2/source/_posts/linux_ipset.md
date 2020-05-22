---
title: linux修改ip设置
date: 2017-09-27 16:54:52
tags: [linux]
categories:
- linux
---
有时候需要修改linux的ip,怕记不住，做个笔记，解决办法如下：

## step1(进入修改文件): 
```
sudo vim /etc/config/networking/interfaces
or
sudo vim /etc/network/interfaces
```

## step2(修改文件): 
```python
auto lo
#本地回环接口
iface lo inet loopback
auto eth0
#以太网接口
#iface eth0 inet dhcp 
iface eth0 inet static
#连接方式dhcp和固定ip
address 192.168.2.135
#ip地址
gateway 192.168.2.1
#ip网关
netmask 255.255.255.0
#子掩码
```
<!--more-->
## step3(重启):
```
sudo /etc/init.d/networking  restart
```
## step4(ping):
```
ping www.baidu.com

if not:
cat /etc/resolv.conf
修改网关至192.168.2.1
```

