---
title: linux系统里svn的搭建
date: 2016-05-10 19:02:25
tags: [linux,svn]
categories:
- svn
---

Ubuntu 安装/卸载
```
sudo apt-get install svnserve
apt-get remove subversion
```

centos 安装/卸载
```
yum install subversion
yum remove subversion
```
<!--more-->
查看进程
```
ps aux | grep svnserve
```

创建目录
```
mkdir -p /svn/test
```

创建版本库
```
svnadmin create svn/test/
```

svn/svn/ 文件夹包含conf, db,format,hooks, locks, README.txt等文件，说明一个SVN库已经建立。

配置文件：
conf：(前置空格svn checkout会报错)
```
passwd
[users]
# harry = harryssecret (用户名 = 密码)
# sally = sallyssecret
Eg:
root = passwd
```
authz

[groups] 用户组
```
# harry_and_sally = harry,sally (组名 = 用户,用户)
Eg:
admin = root,liecol
```

对一个目录的认证规则:
[/foo/bar] 比如对根目录的认证规则的section为[/],如果将上面的test作为svn的起始目录的话，则是[test:/]
[test:/]这种适合于多仓库情况，[/]适合于单仓库情况。

设置单用户的认证规则时一个用户一行:
```
# root = rw (对该目录，root有读写权限)
# liecol = r (对该目录，liecol有读权限)
```

如果使用group，需要在group名字前加@,如
@admin=rw　　(组admin中的用户均为rw，等价于上边的两句话)

(* 表示除了上面的人，比如 * = r，就是除了上面的root和liecol，其他人的权限是对test拥有读的权限。* = 的意思是其他人对test都没有权限)
```
# * =
vi svnserve.conf

anon-access：匿名用户的权限，可以为read，write和none，默认值read。不允许匿名用户访问：anon-access = none

auth-access：认证用户的权限，可以为read，write和none，默认值write。

password-db：密码数据库的路径，去掉前边的# (可自行选填路径，即可多代码库共用同一用户名密码)

authz-db：认证规则库的路径，去掉前边的#。(可自行选填路径，即可多代码库共用同一用户名密码)
```
以上配置需要重启svn

重启svn：

(-d: 表示 svnserve 以“守护”进程模式运行)
(指定文件系统的根位置（版本库的根目录），这样客户端不用输入全路径，就可以访问版本库：如：svn://192.168.0.1/test)
```
svnserve -d -r svn/test/ 
```

checkout访问形式
svn://192.168.0.1/ (svn://ip地址/)

如果是svn目录启动的
svn://192.168.0.1/test (svn://ip地址/test)

如果已启动
根据上面的查看进程:
```
Eg:
ps -ef|grep svn

kill -9 ****
```

如果端口占用
```
svnserve --listen-port 9999 -d -r svn_test1/svn/
```

最后需要导入文件：
Linux:
(–m：message,也就是查看svn log可以看到的注释。)
```
sudo  svn import -m "test"  /home/test  svn://192.168.0.1/test
```

本地：svn

右键关于svn有个import
导入就可以

create By
liecol-晓斌
2016-05-10