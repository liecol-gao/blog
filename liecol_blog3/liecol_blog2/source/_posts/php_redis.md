---
title: php中关于redis的使用
date: 2016-11-23 22:22:00
tags: [php,redis]
categories:
- nosql
---

*安装扩展我这边不做过多说明，大家自行搜索一下。*

php连接到redis服务
```php
<?php
    //连接本地的 Redis 服务
   $redis = new Redis();
   $redis->connect('127.0.0.1', 6379);
   echo "Connection to liecol server.";
         //查看服务是否运行
   echo "Running: "+ $redis->ping();

   //Connection to liecol server.
   //Running: PONG
   //新建数据
   $redis->set("liecol", "name");
   $arList = $redis->get("liecol-list");

   $redis->lpush("liecol1", "Redis");
   $redis->lpush("liecol1", "Mongodb");
   $redis->lpush("liecol1", "Mysql");
   $arList = $redis->lrange("liecol1", 0 ,5);

   $arList = $redis->keys("*");
   //liecol
   //liecol1
?>
```
<!--more-->
几个常用的命令：
setex 带生存时间的写入值
```php
$redis->setex('key', 3600, 'value'); // sets key → value, with 1h TTL.
```

setnx 判断是否重复的，写入值
```php
$redis->setnx('key', 'value');
$redis->setnx('key', 'value');
```

delete  删除指定key的值
返回已经删除key的个数（长整数）
```php
$redis->delete('key1', 'key2');
$redis->delete(array('key3', 'key4', 'key5'));
```

ttl
得到一个key的生存时间

exists
判断key是否存在。存在 true 不在 false

lSize
```php
$redis->lSize('key');
```
返回名称为key的list有多少个元素


以下为php-redis中文文档(内含命令介绍)
http://bbs.redis.cn/forum.php?mod=viewthread&tid=481

create By 
liecol-晓斌 
2016-11-23

