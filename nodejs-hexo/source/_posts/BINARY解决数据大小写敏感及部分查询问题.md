---
title: BINARY解决数据大小写敏感及部分查询问题
date: 2016-07-14 17:09:27
categories: 
tags: 
---

BINARY不是函数，是类型转换运算符，它用来强制它后面的字符串为一个二进制字符串，可以理解为在字符串比较的时候区分大小写

代码如下:

```mysql
mysql> select binary 'ABCD'='abcd' COM1, 'ABCD'='abcd' COM2;
+--------+-----------+
| COM1 | COM2 |
+--------+-----------+
|      0     |      1      |
+---------+-----------+
1 row in set (0.00 sec)

```

(仅仅有些而已！4.*以前)
因为有的MySQL特别是4.*以前的对于中文检索会有不准确的问题，可以在检索的时候加上binary。
建表：

代码如下:

```mysql
create TABLE usertest (
id int(9) unsigned NOT NULL auto_increment,
username varchar(30) NOT NULL default '',
primary key (id)
)
```

插入数据：

代码如下:

```mysql
insert into usertest (username) VALUES('美文');
insert into usertest (username) VALUES('美国项目');
insert into usertest (username) VALUES('李文');
insert into usertest (username) VALUES('老唐');
insert into usertest (username) VALUES('梦漂');
insert into usertest (username) VALUES('龙武');
insert into usertest (username) VALUES('夏');
```

例如：select * from usertest where username like '%夏%' ，结果七条记录都出来了，比较郁闷。
如果使用=而不是like的时候，select * from usertest where username = '夏' ，只出现一个结果。因为mysql 的LIKE操作是按照ASCII 操作的，所以LIKE的时候是可能有问题的。问题继续：如果再加上：

代码如下:

```mysql
insert into usertest (username) VALUES('文');
insert into usertest (username) VALUES('唐');
```
还是使用select * from usertest where username = '夏' ，结果还是出现3条记录，又郁闷了。解决办法如下：
1.在create的时候就使用binary，而不是在query的时候加。

代码如下:

```mysql
username varchar(30) BINARY NOT NULL default '', 如果表已经建好了，使用：
alter table usertest modify username varchar(32) binary; 来就该表的属性。
```
2.在query的时候加上binary，select * from usertest where username like binary  '%夏%' ，就可以准确的查询出一条记录来。

char使用固定长度的空间进行存储，char(4)存储4个字符，根据编码方式的不同占用不同的字节，gbk编码方式，不论是中文还是英文，每个字符占用2个字节的空间，utf8编码方式，每个字符占用3个字节的空间。
如果需要存储的字符串的长度跟所有值的平均长度相差不大，适合用char，如MD5。
对于经常改变的值，char优于varchar，原因是固定长度的行不容易产生碎片。
对于很短的列，char优于varchar，原因是varchar需要额外一个或两个字节存储字符串的长度。

varchar保存可变长度的字符串，使用额外的一个或两个字节存储字符串长度，varchar(10),除了需要存储10个字符，还需要1个字节存储长度信息(10),超过255的长度需要2个字节来存储
例外：Myisam引擎中使用ROW_FORMAT=FIXED时，每行使用相同的空间，造成浪费

char和varchar后面如果有空格，char会自动去掉空格后存储，varchar虽然不会去掉空格，但在进行字符串比较时，会去掉空格进行比较。 