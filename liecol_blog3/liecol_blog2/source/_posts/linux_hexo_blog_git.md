---
title: 如何在linux上使用hexo搭建git博客
date: 2016-09-30 17:48:37
tags: [linux,nodejs,hexo]
categories:
- hexo
---

之前在windows上安装了hexo推送github，但是由于经常不在一个电脑上操作，修改比较麻烦
所以为了偷懒，在linux服务器上也装了一个，方便使用。
中间遇到一些和windows上不同的问题，在这做一些相应的记录。
```
sudo apt-get install git
```
由于我的是Ubuntu,所以最初我采用了apt-get的方式。
当时我可能由于长时间没有安装一些软件，导致报错：
```
failed to fetch from registry: hexo
```
如果有朋友同样遇到，解决办法为：
<!--more-->
```
先update一下：sudo apt-get update
```
安装nodejs：
我当时也采用的是apt-get方式，不过后面就报npm的问题，之后我便卸载，采用curl的模式：
```
curl -sL https://deb.nodesource.com/setup | sudo bash -
```
之后再
```
sudo apt-get install -y nodejs
```
则没有再出现npm的问题
安装hexo
最初我以为和windows上显示一样
```
sudo npm install -g hexo
```
采用这种方式，但是却显示此包不再维护：This package is no longer maintained
我就搜了一下，发现需要：
```
sudo npm install hexo-cli -g
```
hexo 成功安装
然后再mkdir到需要放置的路径下
初始化hexo：
```
hexo init
```
在这我不做hexo内容描述，以后有时间会写hexo的一些细节。
生成静态页：
```
hexo generate(hexo g)
开启服务：
```
````
hexo server
```
如果发现进程被占用
```
hexo server -p 进程号
```
如果发现服务ok，则hexo基本没啥问题了，都是根据需要处理细节
安装git：
最初我同样采用apt-get发现也并不好用,可能因为版本太老了，hexo deploy显示没有找到git
```
sudo apt-get install git
```
于是我又开始卸载
继续采用第二种方式：
```
wget http://soft.itbulu.com/git/git-2.4.6.tar.gz
```
下载包后开始解压
```
tar -zxvf git-2.4.6.tar.gz
```
进入目录开始安装
```
cd git-2.4.6/
make prefix=/usr/local/git all(我将其安装在/usr/local/git目录下)
sudo make prefix=/usr/local/git install
```
git --version查看一下版本，发现并不是2.4.6,它默认使用了"/usr/bin"下的git
```
$ whereis git
git: /usr/bin/git /usr/local/git /usr/share/man/man1/git.1.gz
```
只能修改环境变量，替换"/usr/bin"下的git，可以修改“/etc/profile”文件
```
sudo vim /etc/profile
```
找到PATH，追加
```
PATH=/usr/local/git/bin
```
source命令应用修改。
```
source /etc/profile
```
版本搞定，再次去hexo d发现换了个错，其实是忘了执行：
```
sudo npm install hexo-deployer-git --save
```
友情提醒:hexo里git的地址不能写错了：
```
repo: https://github.com/git-name/git-name.github.io.git
```
再次提交终于搞定，一波三折。
create by
liecol-晓斌
2016-09-30
