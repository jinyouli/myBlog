---
title: 利用Cycript 操作运行时
date: 2017-09-05 15:01:37
categories:
tags: iOS逆向工程
---
今天来介绍攻击iOS软件的好工具：Cycript。<!-- more -->

Cycript 是由saurik 推出的一款脚本语言，介于Object-C 和JavaScript 之间的编程语言。Cycript工具完全兼容javascript ，你可以使用完整的javascript语法来编写程序。同时也可以直接操作Object-C语言的组件。

官网地址：http://www.cycript.org/

首先，用Cydia 安装mobilesubstrate和adv-cmds 这两个包，安装后会要求重启。重启后，使用ssh 登录到设备。登录方式http://blog.csdn.NET/woaizijiheni/article/details/49181295这篇文章有介绍。

然后使用dpkg安装，$dpkg -ipkgName

安装完成后，我们就来使用它。

 
```
# cycript 

cy# 
```

 按control-D 退出

接下来，就来使用

cycript 中可以使用javascript 形式的变量定义与Cocoa类连接。来看一个具体的例子：

```

cy# var mystring =[[NSString alloc]initWithString:@"helloworld!!"];

@"hello world!!"
```
 

这语法。。。。。

再来看看cycript 如何定义方法

```
cy# function range(a,b){

cy> var q =[];

cy> for(var i = a;i != b;i++)

cy> q.push([[NSNumber numberWithInt:i]boolValue]);

cy> return q;

cy> }

cy# range(0,10)

[0,1,1,1,1,1,1,1,1,1]
```
 

Cycript 还可以访问Object-C运行时环境和任何对象。下面用例子说明

```
cy# var mystring =[[NSString alloc]initWithString:@"HelloWorld!!"];

@"Hello World!!"

cy# [mystring length]

13

cy# [mystring characterAtIndex:2]

108（l）

不仅如此，还可以像其他语言一样对文件进行操作

cy# var mystring = sel.call(new NSString,@"Hello world!!")

@"Hello world!!"

# cat output.txt 

Hello World!!

Cycript 还可以使用选择器（selector）

cy# var sel =@selector(initWithString:)

@selector(initWithString:)

cy# var mystring = sel.call(new NSString,@"Hello world!!")

@"Hello world!!"

```

当然，光这样操作有什么意思，总得用它来做点事情。

它的最强大的特征之一便是可以通过-p参数附着（attach） 到正在运行的进程上，并借此进入该软件的运行时环境进行操作。


现在这个手机上有个叫PhotoVault 的软件，是用来保护照片，在每次进入这个app内浏览照片时，需要输入pin 密码，看看能不能使用Cycript 对其做点什么呢？

每次进入总是要输入密码，那有什么办法可以在进入之前不需要输入密码。

用正向开发的思维来思考，在进入之前输入密码，便是在AppDelegate.m中进行操作了。

先试着反编译出来看看，分析分析，找个突破点

反编译方法，具体操作在这篇文章 中 有介绍  http://blog.csdn.net/woaizijiheni/article/details/49612849

反编译后，拿到的它的.h 文件有很多，先找到AppDelegate.h文件 看看

 

打开AppDelegate.h 发现里面方法不是很多，

-(void)resumePatternLock;

-(void)showPatternLock;

-(void)showPhotoGallery;

这三个方法最可疑了，从方法命名看，-(void)showPhotoGallery;这个方法有最大可能性。尝试着看看

```
# ps aux |grep Application

mobile    5328   3.1 6.5   713104  33560   ??  Ss    3:57PM  0:15.71/var/mobile/Containers/Bundle/Application/E94E1C76-E3A1-4D6F-8459-E7056B0310BA/PhotoVault.app/PhotoVault

 

# cycript -p 5328

cy# var app =[^C

cy# var app =[UIApplication sharedApplication]

#"<UIApplication: 0x14dc5b30>"

cy# var delegate =new Instance(^C

cy# app.delegate

#"<AppDelegate: 0x14ebb860>"

cy# var delegate =new Instance(0x14ebb860)

#"<AppDelegate: 0x14ebb860>"

cy# [delegate showPhotoGallery]
```
发现现在app 直接进入主界面了，那就是它了。

原文地址:http://blog.csdn.net/woaizijiheni/article/details/49794367

