---
title: Mac打开NTFS硬盘
date: 2017-08-08 14:52:03
categories:
tags:
---
MAC电脑里的数据满了，想拷贝到移动硬盘，无奈NTFS不能直接复制，<!-- more -->之前用了第三方的Mounty现在不知何故已经不再有效，在官网上下载的也是如此，还有一款Paragon的第三方软件又要收费，下面引用了知乎的答案，亲试有用，第一种方法。
原文：https://www.zhihu.com/question/19571334

-------------------------------------------------------------------------------------------------
搜了好多，终于搞定了，如下：
系统：OS X El Capitan 10.11.3
MacBook Pro(Mid 2012)
下面是过程，最后可以成功读写。没有安装别的软件。
0）将移动硬盘插入电脑，打开terminal，输入：
diskutil list

会有如下类似输出：
```
/dev/disk0 (internal, physical):
#: TYPE NAME SIZE IDENTIFIER
0: GUID_partition_scheme *1.0 TB disk0
1: EFI EFI 209.7 MB disk0s1
2: Apple_HFS Macintosh HD 900.3 GB disk0s2
3: Apple_Boot Recovery HD 650.0 MB disk0s3
4: Microsoft Basic Data BOOTCAMP 99.0 GB disk0s4
/dev/disk1 (external, physical):
#: TYPE NAME SIZE IDENTIFIER
0: FDisk_partition_scheme *2.0 TB disk1
1: Windows_NTFS TOSHIBA 2.0 TB disk1s1
```
其中，最后一行的TOSHIBA即为移动硬盘的名字，这个名字后面会用到。disk1s1为该移动硬盘的所在分区。

1）将移动硬盘的名字改成不带有空格的。比如"TOSHIBA", 不能是"TOSHIBA EXT"这种形式的。【特别重要】
2）在/etc/fstab中添加如下一行：
```
LABEL=TOSHIBA none ntfs rw,auto,nobrowse
```
硬盘名字 注意逗号后面无空格如果没有这个文件，就自己创建一个，如果没有权限，就用sudo; 比如
```
cd /etc/
sudo vi fstab
```
【注意】有人说，如果名字之间有空格比如'TOSHIBA EXT',那么添加的这一行就应该是：
```
LABEL=TOSHIBA\040EXT none ntfs rw,auto,nobrowse
```
但是我试过了，不能成功。

3)然后推出移动硬盘，拔出移动硬盘，再重新插上移动硬盘。这时候，桌面上不再用移动硬盘图标，这时候我们打开terminal,进入/Volumes/文件夹就能看到了。
```
cd /Volumes
```
我们就能看到TOSHIBA这个目录了。
```
cd /Volumes/TOSHIBA
open .
```
就可以打开移动硬盘。直接可以读写。
完毕。

----------------------------------2016-06-06更新-----------------------------------有人反映在fstab中添加这一行“LABEL=TOSHIBA none ntfs rw,auto,nobrowse”有时候会不管用，硬盘不仅不在桌面上显示，而且依然没有写权限， @Lee Kevin

给出了方法，说直接使用硬盘的UUID，我试了一下，可用，将方法一并放到这里，一共有两步：
1）查询硬盘的UUID
2）在fstab中添加配置文字

1）查询硬盘的UUID
打开terminal, 输入：
diskutil info /Volumes/TOSHIBA

会出现包含UUID信息的一大段输出，其中，
```
Volume UUID: B83735C0-40A9-478B-9689-FD98941041C3
```
这一行的B83735C0-40A9-478B-9689-FD98941041C3就是硬盘TOSHIBA的UUID，下面会用到。

2）在fstab中添加这一行：
UUID=B83735C0-40A9-478B-9689-FD98941041C3 none ntfs rw,auto,nobrowse(特别注意，逗号“，”后面没有空格）

3）推出硬盘，重新插入，发现桌面上没有硬盘图标，进入terminal:
```
cd /Volumes/
ls
```
就可以看到TOSHIBA了
可以用ln -s命令在桌面上创建TOSHIBA的软链接：
```
mkdir -p ~/Desktop/toshiba (在桌面上创建目录toshiba)

ln -s /Volumes/TOSHIBA ~/Desktop/toshiba
```
然后我们就可以在桌面上直接访问TOSHIBA硬盘了。

-----------------------20160610更新----------------------------
注：查询UUID
1）在terminal下使用命令：diskutil list
大段的输出：
![](http://upload-images.jianshu.io/upload_images/977602-c01c2d4dd6a67b76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)2）可以使用名字（NAME)或者分区ID（IDENTIFIER)查询UUID（是Volume UUID)
![](http://upload-images.jianshu.io/upload_images/977602-c01c2d4dd6a67b76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)2）可以使用名字（NAME)或者分区ID（IDENTIFIER)查询UUID（是Volume UUID)
使用分区ID：
![](http://upload-images.jianshu.io/upload_images/977602-f8810e21710dc8ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)我们就能看到Volume UUID了。
![](http://upload-images.jianshu.io/upload_images/977602-f8810e21710dc8ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)我们就能看到Volume UUID了。
使用NAME之前已经用过了，就不说了。
================更新2016-10-19===================
之前用过票数最高的 @叶文

的方案，但是会出现问题，但最后终于解决，我把 @叶文

 整个过程写一下：
```
terminal:
$sudo -s #切换root用户
$cd /sbin #进入/sbin文件夹
$mv mount_ntfs mount_ntfs_orig#重命名mount_ntfs为mount_ntfs_orig
【这一步会出现问题：mv: rename mount_ntfs to mount_ntfs_orig: Operation not permitted. google了一下，找到解决方法，见后面】
$vim mount_ntfs #（新建）编辑mount_ntfs脚本文件，内容为：
#!/bin/sh
/sbin/mount_ntfs_orig -o rw,nobrowse "$@"
#保存，退出
$chmod a+x mount_ntfs #修改权限增加可执行
$exit #退出root身份

具体如下：
1）重启mac，cmd+R进入恢复（recovery）模式
2）找到terminal(在“XX工具”里面）
3）输入：
$csrutil disable
4）输入：
$reboot
```
重启。

mv: rename mount_ntfs to mount_ntfs_orig: Operation not permitted. 问题的解决，参照：[osx - Operation Not Permitted when on root El capitan (rootless disabled)**](https://link.zhihu.com/?target=http%3A//stackoverflow.com/questions/32659348/operation-not-permitted-when-on-root-el-capitan-rootless-disabled)

------------------------更新完毕------------------------------

