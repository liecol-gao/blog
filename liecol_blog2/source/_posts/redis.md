---
title: redis操作浅谈
date: 2016-11-23 19:30:00
tags: [linux,redis,nosql]
categories:
- nosql
---

**介绍：**
Redis 开源高性能的`key-value`数据库，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。
读的速度是110000次/s,写的速度是81000次/s。
`redis`支持string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)
这里不做安装介绍，有需要的可自行搜索教程。
Redis 客户端语法：`redis-cli`
启动 redis 客户端，打开终端并输入命令 redis-cli.
```php
$redis-cli
redis 127.0.0.1:6379>
redis 127.0.0.1:6379> PING
PONG
```
<!--more-->
远程服务：
```php
$redis-cli -h host -p port -a password
eg: $redis-cli -h 127.0.0.1 -p 6379 -a "mypass"
```

redis key键
```php
redis 127.0.0.1:6379> SET liecol redis
OK
redis 127.0.0.1:6379> DEL liecol
(integer) 1
```

这里说几个常用的，其他的自行搜索redis键的一些命令
DEL key 该命令用于在 key 存在是删除 key。
DUMP key 序列化给定 key ，并返回被序列化的值。
EXISTS key 检查给定 key 是否存在。
EXPIRE key seconds 为给定 key 设置过期时间。
PERSIST key 移除 key 的过期时间，key 将持久保持。
RENAME key newkey 修改 key 的名称

value类型：
string（字符串）
```php
redis 127.0.0.1:6379> GET liecol redis
"redis"
```
这里说几个常用的，其他的自行搜索redis字符串的一些命令
SET key value 设置指定 key 的值
GET key 获取指定 key 的值。
STRLEN key 返回 key 所储存的字符串值的长度。
MSET key value [key value ...] 同时设置一个或多个 key-value 对。

Redis hash (hash特别适合用于存储对象)
```php
redis 127.0.0.1:6379> HMSET liecol name "redis tutorial" description "redis basic commands for caching" likes 20 visitors 23000
OK
redis 127.0.0.1:6379> HGETALL liecol
 
1) "name"
2) "redis tutorial"
3) "description"
4) "redis basic commands for caching"
5) "likes"
6) "20"
7) "visitors"
8) "23000"
```

HDEL key field2 [field2] 删除一个或多个哈希表字段
HEXISTS key field 查看哈希表 key 中，指定的字段是否存在。
HGET key field 获取存储在哈希表中指定字段的值
HGETALL key 获取在哈希表中指定 key 的所有字段和值
HVALS key 获取哈希表中所有值

Redis列表
```php
redis 127.0.0.1:6379> LPUSH liecol name1
(integer) 1
redis 127.0.0.1:6379> LPUSH liecol name2
(integer) 2
redis 127.0.0.1:6379> LPUSH liecol name3
(integer) 3
redis 127.0.0.1:6379> LRANGE liecol 0 10
1) "name1"
2) "name2"
3) "name3"
```

LINDEX key index 通过索引获取列表中的元素
LINSERT key BEFORE|AFTER pivot value 在列表的元素前或者后插入元素
LLEN key 获取列表长度
LPOP key 移出并获取列表的第一个元素
LPUSH key value1 [value2] 将一个或多个值插入到列表头部
LREM key count value 移除列表元素
LRANGE key start stop 获取列表指定范围内的元素

Redis的Set (集合中不能出现重复的数据)
```php
redis 127.0.0.1:6379> SADD liecol name1
(integer) 1
redis 127.0.0.1:6379> SADD liecol name2
(integer) 1
redis 127.0.0.1:6379> SADD liecol name3
(integer) 1
redis 127.0.0.1:6379> SADD liecol name3
(integer) 0-------------(集合中不能出现重复的数据)
redis 127.0.0.1:6379> SMEMBERS liecol
1) "name1"
2) "name2"
3) "name3"
```

SADD key member1 [member2] 向集合添加一个或多个成员
SCARD key 获取集合的成员数
SDIFF key1 [key2] 返回给定所有集合的差集
SINTER key1 [key2] 返回给定所有集合的交集
SISMEMBER key member 判断 member 元素是否是集合 key 的成员
SMEMBERS key 返回集合中的所有成员
SREM key member1 [member2] 移除集合中一个或多个成员
SUNION key1 [key2] 返回所有给定集合的并集

事务：
```php
MULTI
SET liecol "test"
GET liecol
SADD x x
LPUSH y y
HGETALL z z
EXEC
```

DISCARD 取消事务，放弃执行事务块内的所有命令。
EXEC 执行所有事务块内的命令。
MULTI 标记一个事务块的开始。

redis 性能测试：
```php
redis-benchmark [option] [option value]
eg: redis-benchmark -n 100000
```
1	-h	指定服务器主机名	127.0.0.1
2	-p	指定服务器端口	6379
3	-s	指定服务器 socket	
4	-c	指定并发连接数	50
5	-n	指定请求数	10000
6	-d	以字节的形式指定 SET/GET 值的数据大小	2
7	-k	1=keep alive 0=reconnect	1
8	-r	SET/GET/INCR 使用随机 key, SADD 使用随机值	
9	-P	通过管道传输 <numreq> 请求	1
10	-q	强制退出 redis。仅显示 query/sec 值	
11	--csv	以 CSV 格式输出	
12	-l	生成循环，永久执行测试	
13	-t	仅运行以逗号分隔的测试命令列表。	
14	-I	Idle 模式。仅打开 N 个 idle 连接并等待。

Eg:
```php
redis-benchmark -h 127.0.0.1 -p 6379 -t set,lpush -n 100000 -q
SET: 146198.83 requests per second
LPUSH: 145560.41 requests per second
```
以上实例中主机为 127.0.0.1，端口号为 6379，执行的命令为 set,lpush，请求数为 10000，通过 -q 参数让结果只显示每秒执行的请求数。


无序在这里就不做介绍了。大家自行搜索一下，以下为整理好的redis命令地址：
http://doc.redisfans.com/
http://www.redis.cn/commands.html

create By 
liecol-晓斌 
2016-11-23
