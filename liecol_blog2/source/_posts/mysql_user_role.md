---
title: mysql用户权限管理
date: 2015-11-17 17:56:34
tags: mysql
categories:
- mysql
---

进入mysql
```sql
use mysql
```
创建用户
```sql
CREATE USER 'username'@'host' IDENTIFIED BY 'password'; 
```
username - 用户名, 
host - 主机,如果是本地用户可用localhost, 任意远程,可以使用通配符%.
password - 密码,可以为空
<!--more-->
```sql
CREATE USER 'root'@'localhost' IDENTIFIED BY '123456';
CREATE USER 'root'@'192.168.1.101_' IDENDIFIED BY '123456';
CREATE USER 'root'@'%' IDENTIFIED BY '123456';
CREATE USER 'root'@'%' IDENTIFIED BY '';
CREATE USER 'root'@'%';
```
用户授权
```sql
GRANT privileges ON databasename.tablename TO 'username'@'host'
```
privileges 用户操作权限SELECT , INSERT , UPDATE delete等(全部采用all)
databasename 数据库
tablename 表名
(如果要授权所有的数据库名下的所有表名则*.*)
username 用户名
host 可登录主机
```sql
GRANT SELECT, INSERT ON test.user TO 'root'@'%';
GRANT ALL ON *.* TO 'root'@'%'; 
```
用以上命令授权的用户不能给其它用户授权,如果想让该用户可以授权,用以下命令:
```sql
GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION; 
```
撤销权限
```sql
REVOKE privilege ON databasename.tablename FROM 'username'@'host';
```
如何授权则如何撤销，不能混淆，否则是无作用。

查看权限信息
```sql
SHOW GRANTS FOR 'root'@'%'
```
查看自己的权限
```sql
show grants;
```
删除用户
```sql
DROP USER 'username'@'host'; 
```
修改用户名字
```sql
rename user root to newroot
```
设置密码
```sql
set password for zx_root =password('xxxxxx');
```
更改密码
```sql
update mysql.user set password=password('xxxx')  where user='otheruser'
update user set password=PASSWORD(‘123456’) where User='root';
```
设置外链接
```sql
update user set host="%" where user='root' and host = 'locolhost'
```
刷新表权限(让配置生效)
```sql
flush privileges; 
```
启动mysql
```sql
service mysqld start
```
重启mysql
```sql
service mysqld restart
```
关闭mysql
```sql
service mysqld restart
```
开启apache
```sql
service httpd start
```
重启开启apache
```sql
service httpd restart
```
关闭apache
```sql
service httpd stop
```
2015.11.17
By liecol-晓斌