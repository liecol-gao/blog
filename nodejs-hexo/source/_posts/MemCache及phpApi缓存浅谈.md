---
title: MemCache及phpApi缓存浅谈
date: 2016-07-14 17:50:17
tags:
---

Memcache是什么？

Memcache和mysql一样，是一款服务端C/S管理软件，有ip和有端口(C/S需要客户端才能访问，B/S无需第三方软件应用即可访问)

为什么用Memcache？

mysql通过客户端发送硬盘数据

Memcache通过内存发送数据，读取速度快

不过服务如果关闭，数据则会清除，属于临时文件存储所

安装Memcache(linux为例子)

网络教程很多，在这以yum为例子(或者install)
```
yum install memcached.i686
```
另外需要安装libevent,这是安装Memcache的唯一前提条件

-p tcp端口号  默认11211

-m最大内存大小 默认64M

-d做为守护进程在后台运行

-c最大并发连接数

-P保存Memcache的pid文件

-l监听服务器地址

-u 如果以root用户启动MemCache必须加此选项

查看运行状态信息

stats

数据管理指令(采用哈希算法，增删改查等操作)

指令格式

命令，键，标记，有效期，数据长度

add/set/delete/get/replace

关闭Memcache
```
Kill  进程号
```

Memcache(phpApi)

这里就不写如何安装了，网络版本很多(笔者比较懒)

步骤为：下载，解压，安装，安装扩展，修改ini，phpinfo查看是否安装成功

php连接和关闭
```php
$memcached = new Memcache

$memcached -> connect('host',11211)连接host

$memcached->set("test","houdunwang.com",0,20);设置test数据

echo $memcached->get("test");获取test

$memcached->delete(test', 10);10秒后删除

$memcached->flush();清空缓存

$memcached -> close()
```

参数

Host主机地址

Portmemcached服务端监听端口

Timeout连接持续（超时）时间，单位秒。默认值1秒


多服务器设置连接
```php
$conf = array(

array('host'=>192.168.0.1,'port'=>'11211')

array('host'=>192.168.0.2,'port'=>'11211')

array('host'=>192.168.0.3,'port'=>'11211')

)

foreach ($conf as $v){

$memcached->addserver($v['host'],$v['port'])

}
```
addserver一共有八个参数，除第一个参数外，其他均是可选，第一为host地址

在这笔者就不一一列举了(或者等闲时添上)

在这笔者写个例子(php为例):

```php

function select($sqlstr,Memcache $memcache){

$data = $memcache->get($sqlstr)

if(!data){

try{

$mysql/pdo=>>>>查询

}catch{

die/error("...")

}

$memcached ->add($sqlstr,$data,IYHHB ,0)

键名，键值，是否压缩，是否过期(0为永不过期)

}

return $data

}

$memcache = new Memcache;

$memcache ->connect('localhost',11211)

$data = select("select * from user",$memcache)

var_dump($data)
```

by liecol-晓斌

2015.09.28