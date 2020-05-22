---
title: 挂载磁盘和永久挂载
date: 2016-12-23 18:02:15
tags: linux
categories:
- linux
---

最近折腾，给磁盘扩容，阿里云得从新挂载，顺道儿这边做个记录。

df -hl(看看有没有挂载成功)
```
root@iZ25xadwwi4Z:~# df -hl
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1       20G  3.4G   16G  18% /
udev            489M  4.0K  489M   1% /dev
tmpfs           199M  268K  199M   1% /run
none            5.0M     0  5.0M   0% /run/lock
none            498M     0  498M   0% /run/shm
```
<!--more-->
fdisk -l (看看挂载分区，下面是我的)
```
Disk /dev/xvdb: 42.9 GB, 42949672960 bytes
255 heads, 63 sectors/track, 5221 cylinders, total 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xb5efb014

Device Boot          Start         End      Blocks   Id  System
/dev/xvdb1            2048    40000000    19998976+  83  Linux
/dev/xvdb2        40000001    83886079    21943039+  83  Linux
```
fdisk /dev/path(磁盘路径)
```
Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)
```
顺序：
m
|
n
|
p
分配和设置大小

分配完成
```
Command (m for help): wq
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
mkfs.ext3 /dev/xvdb1 创建文件系统(需要为其指定一种文件系统才能用来存储数据，这边ext3，可以-t查看)
```
root@iZ25xadwwi4Z:~# mkfs.ext3 /dev/xvdb1
mke2fs 1.42 (29-Nov-2011)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
1250928 inodes, 4999744 blocks
249987 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=4294967296
153 block groups
32768 blocks per group, 32768 fragments per group
8176 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```
挂载文件系统
这边不能选择已有文件，否则会被覆盖掉。
mount(临时挂载，好久没挂，我之前忘了重启就没了，到这就结束了。)
```
mkdir /liecol
mount /dev/xvdb1 /liecol/
```
查看一下:
```
root@iZ25xadwwi4Z:~# ls -l /liecol/
total 16
drwx------ 2 root root 16384 Dec 23 20:42 lost+found
```
这一步，别忘了(永久挂载)：
```
echo "/dev/xvdb1 /liecol ext3 defaults 1 2" >> /etc/fstab
```
别和我一样糟糕了。

df -hl 查看一下，再重启试试看就差不多了。

create By liecol
2016/12/23