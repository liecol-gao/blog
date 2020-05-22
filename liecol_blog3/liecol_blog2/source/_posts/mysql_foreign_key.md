---
title: Mysql 外键 Foreign Key
date: 2018-04-25 10:02:34
tags: mysql
categories:
- mysql
---

昨晚被朋友问到一个有关mysql外键的问题,问我删除外键再重新创建另一张表的外键，会提示key值重复，一下子问懵了，
潜意识告诉我不应该，但是自己做了一下操作确实如此，最后发现是建立了索引的缘故。故，在此记录一下，写一下关于我的理解。
既然做记录，这边就简述一下外键：
* 如果一张表中有一个非主键的字段指向了别一张表中的主键，就将该字段叫做外键。
* 1.对子表(外键所在的表)的作用：子表在进行写操作的时候，如果外键字段在父表中找不到对应的匹配，操作就会失败。
* 2.对父表的作用：对父表的主键字段进行删和改时，如果对应的主键在子表中被引用，操作就会失败。
* 外键的定制作用：
    => district：严格模式(默认), 父表不能删除或更新一个被子表引用的记录。
    => cascade：级联模式, 父表操作后，子表关联的数据也跟着一起操作。
    => set null：置空模式,前提外键字段允许为NLL,  父表操作后，子表对应的字段被置空。
* 使用外键的前提：
    => 表储存引擎必须是innodb，否则创建的外键无约束效果。
    => 外键的列类型必须与父表的主键类型完全一致。
    => 外键的名字不能重复。
    => 已经存在数据的字段被设为外键时，必须保证字段中的数据与父表的主键数据对应起来。

其实在现在的并发系统中，因为限制的原因，采用外键的机会个人觉得不会很多.(笔者参与的几个项目均未采用外键)
接下来，我们做一个验证：
<!--more-->
```sql
CREATE TABLE `tab_father` (  
    `t_first` INT(11) UNSIGNED NOT NULL,  
    `t_second` INT(11) UNSIGNED NOT NULL,  
    `ts` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP  
)  
COLLATE='utf8_general_ci'  
ENGINE=InnoDB;  
```
在上面建立的这个`tab_father`表里，并未建立索引，也同样没有建立主键, 接下来再建一张子表(`tab_child`)：
```sql
CREATE TABLE `tab_child` (  
    `id` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,  
    `col` INT(10) UNSIGNED NOT NULL,  
    `col2` INT(10) UNSIGNED NOT NULL, 
    PRIMARY KEY (`id`)  
)  
COLLATE='utf8_general_ci'  
ENGINE=InnoDB  
```
再进行子表`col`关联父表`t_second`字段，发现失败。
```sql
ALTER TABLE `tab_child` ADD CONSTRAINT `FK_tab_child_tab_father` FOREIGN KEY (`col`) 
REFERENCES `tab_father` (`t_second`) ON UPDATE CASCADE ON DELETE CASCADE;
[Err] 1215 - Cannot add foreign key constraint
```
为`tab_father`添加一个索引再执行外键语句 ->(成功)：
```sql
ALTER TABLE `tab_father` ADD INDEX `t_second` (`t_second`); 
```
##### <span style='color:red; text-decoration:underline;'>注：这个时候，我们去看子表的索引，会发现，其实在创建外键的同时，mysql默认给子表也添加了一个索引。</span>
```sql
+------------+-------------+------------+
| TABLE_NAME | COLUMN_NAME | COLUMN_KEY |
+------------+-------------+------------+
| tab_child  | col         | MUL        |
+------------+-------------+------------+
```
我们再创建一个子表tab_child2：
```sql
CREATE TABLE `tab_child2` (  
    `id` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,  
    `col` INT(10) UNSIGNED NOT NULL,  
    `col2` INT(10) UNSIGNED NOT NULL, 
    PRIMARY KEY (`id`)  
)  
COLLATE='utf8_general_ci'  
ENGINE=InnoDB
```
删掉`tab_child`的外键，再给`tab_child2`做`col2`关联父表`t_first`字段，发现失败。
```sql
ALTER TABLE tab_child drop foreign key FK_tab_child_tab_father;
ALTER TABLE `tab_child2` ADD CONSTRAINT `FK_tab_child_tab_father` FOREIGN KEY (`col2`) 
REFERENCES `tab_father` (`t_first`) ON UPDATE CASCADE ON DELETE CASCADE;
[Err] 1215 - Cannot add foreign key constraint
```
笔者朋友就是在这里被卡住的，为什么已经drop了外键，但是却报错了呢，原因同之前一样，因为父表的t_first，未建立索引，建立索引后成功。
笔者朋友还遇到一种情况：
```sql
[Err] 1061 - Duplicate key name 'FK_tab_child_tab_father'
```
这种也令人费解，同样的也是为什么已经drop了外键，但是也报错了呢，这个是因为，在建立外键时，
子表被自动加了`FK_tab_child_tab_father`这个索引名称导致后面在建立外键时，采用同一个名称，所以会报错。
<span style='color:red; text-decoration:underline;'>这个时候，可以修改外键名称，或删除之前没用的索引，或在最开始创建一个字段索引(注: 增加外键是无则加之。)，这样的办法来进行增加外键。</span>
那么，为什么mysql会在创立外键的时间，给默认增加一个索引呢？
主要原因就在于查询性能。
父子表之间在进行外键检查时，需要一条一条每条必查地校验，而且在对父表的SELECT操作时，为了避免产出数据不一致，
使用的是一致性锁定读(SELECT …… LOCK IN SHARE MODE)，主动加一个S锁阻塞其他的修改操作。如果没有索引，
对于大一点的表而言那效率就非常低下。参考文档：https://dev.mysql.com/doc/refman/5.7/en/create-table-foreign-keys.html

那为什么子表可以隐式地创建索引而父表需要显式地创建(主表没有索引报错)？
* 笔者理解为是mysql的执行顺序问题(性能问题)。
    检查父表关联字段有没有创建可使用的有效索引
    检查子表父表字段属性的一致性，包括数据类型、长度、字符编码等
    检查子表外键字段是否创建索引(无则创建，创建名称唯一)
    检查子表数据是否关联父表记录
* 可能会存在其他原因：
    父表权限(暗示拥有创建字表的权限)
    操作权限(外键是基于字表上工作)
或者这是mysql的设计者觉得这是一种比较合理的方式吧。