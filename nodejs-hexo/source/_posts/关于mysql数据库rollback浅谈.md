---
title: 关于mysql数据库rollback浅谈
date: 2016-07-14 17:41:11
tags:
---
```
/**
  *    create By liecol 2015.04.02.
 */
```
由于公司新项目开启，数据库搜索引擎从myisam改成了innodb，于是便也能支持了事务，由于需要笔者今儿写了一个function，可是在回滚的时候却出现了问题，耗了些时间才找出原因，于是决定需要写个文档，以便自己或者同行朋友免得走了弯路。废话不说，不喜勿喷，代码如下：
mysql数据库rollback需要具备以下几个条件：
1：数据库：ENGINE=InnoDB 判断搜索引擎为InnoDB，default是否开启(Yes)因为只有Innodb才能支持事务。
   ```
   mysql> show engines;
   mysql> show variables like '%storage_engine%';
	```
2: 数据表：ENGINE=InnoDB 笔者在这遇到了一些问题，只是看了数据库ENGINE是否是InnoDB，而为了省事儿没看数据表的存储引擎，事务支持需要库和表ENGINE都是InnoDB。
   ```
   mysql> show create table 表名;
   mysql> alter table 表名 engine=innodb;
   ```
3: 设置系统事务隔离级别
    ```
    mysql> SELECT @@global.tx_isolation;(查看系统事务隔离级别)
    +-----------------------+
    | @@global.tx_isolation |
    +-----------------------+
    | REPEATABLE-READ       |
    +-----------------------+
    mysql> set global transaction isolation level read committed;(更改系统事务隔离级别)
	```
4: 设置会话事务隔离级别
    ```
    mysql> SELECT @@tx_isolation;(查看会话事务隔离级别)
    +-----------------+
    | @@tx_isolation  |
    +-----------------+
    | REPEATABLE-READ |
    +-----------------+
    mysql> SELECT @@session.tx_isolation;
    +------------------------+
    | @@session.tx_isolation |
    +------------------------+
    | REPEATABLE-READ        |
    +------------------------+
    mysql> set global transaction isolation level read committed;(更改会话事务隔离级别)
	```
既然提到了隔离级别，那这里笔者说一下系统隔离级别的等级，能力有限，可能总结的不好，各位看官见谅，如果不懂可以在Internet里搜索技术博客可能更能明白一些。
        (1)Read Uncommitted（读取未提交内容）
           在该隔离级别，所有事务都可以看到其他未提交事务的执行结果。本隔离级别很少用于实际应用，因为它的性能也不比其他级别好多少。读取未提交的数据，也被称之为脏读。(个人理解为，前一个事务更新了某个数据，另一个事务随即读取了一个数据，但是因为某些原因前一个rollback了，则后一条数据是读取不正确的。这便是脏读。)
       (2)Read Committed（读取提交内容）
           这是大多数数据库系统的默认隔离级别（但不是MySQL默认的）。它满足了隔离的简单定义：一个事务只能看见已经提交事务所做的改变。这种隔离级别 也支持所谓的不可重复读（Nonrepeatable Read），因为同一事务的其他实例在该实例处理其间可能会有新的commit，所以同一select可能返回不同结果。
        (3)Repeatable Read（可重读）
           这是MySQL的默认事务隔离级别，它确保同一事务的多个实例在并发读取数据时，会看到同样的数据行。不过理论上，这会导致另一个棘手的问题：幻读 （Phantom Read）。简单的说，幻读指当用户读取某一范围的数据行时，另一个事务又在该范围内插入了新行，当用户再读取该范围的数据行时，会发现有新的“幻影” 行。InnoDB和Falcon存储引擎通过多版本并发控制（MVCC，Multiversion Concurrency Control）机制解决了该问题。(个人理解不可重读为：同一个事务里前后两次查询读取的数据不一致，可能是因为他们中间更新了一条数据，而可重读则充分保证读取数据一致。) 
       (4)Serializable（可串行化）
           这是最高的隔离级别，它通过强制事务排序，使之不可能相互冲突，从而解决幻读问题。简言之，它是在每个读的数据行上加上共享锁。在这个级别，可能导致大量的超时现象和锁竞争。(个人理解幻读为：事务里，第一次读取，第二次更新数据，再次选择第一次读取会读到第二次更新的数据，称之为幻读。)
           再深入就得说到锁了。笔者怕露馅啊~哈哈，有机会整理一份，收回来，继续事务流程。

5: 查看autocommit变量(mysql默认是自动提交事务的,这里解释一下，mysql默认为自动commit，而改为0则是手动commit即通过rollback和commit来操作数据表)
    ```
    select @@autocommit;
    set autocommit = 0;
    mysql> select @@autocommit;
    +--------------+
    | @@autocommit |
    +--------------+
    |            0 |
    +--------------+
	```
6: 以上设定就完了，接下来就是rollback操作，分为三步骤。
    (1)使用start transaction;或begin;显示的开启一个事务。
    ```
    mysql> start transaction;/begin;
    ```
    (2)终于到回滚了。这里就是回滚rollback区了。
    ```
    mysql> rollback;
    ```
    (3)不需要回滚则commit;
    ```
    mysql> commit;
	```
7: 因为笔者是用的php程序，在这再延伸一下框架多表rollback，以thinkphp为例：
    ```
    $Model=M("");
    $Model->startTrans();
    $result1=$Model->table("table1")->接下来的语句条件。
    $result2=$Model->table("table2")->接下来的语句条件。
    $Model->rollback();
    $Model->commit();
    如果不做table也可以:
    $Model1=M("table1");
    $Model2=M("table2");
    $Model1/2->startTrans();
    ```
    接下来的callback和commit的操作。
END.