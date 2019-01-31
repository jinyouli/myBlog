---
title: 修改Host文件，让你的Google跑起来
date: 2017-05-04 09:54:12
categories:
tags:
---
hosts文件修改
<!-- more -->


![图片.png](http://upload-images.jianshu.io/upload_images/977602-82758687dee34a5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
近期，相信大家都发现了，国内Google访问狠不给力，基本上打不开，谷歌在相关的服务器也被搬到了美国，这不禁让人感慨,谷歌难道要全面退出中国的节奏？

作为一名“IT界”的淫才，百度往往会让你使用的特别“蛋疼”，针对于专业领域的搜索结果更是鸡肋，针对性不强，垃圾信息多，不精准等会让你浪费很多时间。相对而言，谷歌的搜索结果显示更为客观，尤其在搜索技术性文章的时候，结果更加精准。百度的搜索则更加侧重于中国网民的搜索习惯，搜索结果更加大众化。这就是为什么技术人员更喜欢用谷歌，而百度更符合大众口味的区别。

不能访问谷歌着实让人捉急，今天和大家分享如何通过修改Host文件来打到访问Google、Youtube等国外网站的目的。

一、XP用户

XP的在C盘 C:WINDOWS/system32/drivers/etc 目录下的 hosts文件，我们用记事本打开后 修改里面的内容，添加本文下方附件的内容到host文件中保存即可。

二、Win7、Win8等系统用户

Win7及以后的系统涉及到管理员权限问题，需要用管理员身份运行记事本，再打开Host文件，进行修改，具体步骤如下：

![图片.png](http://upload-images.jianshu.io/upload_images/977602-b7ea26c086370f49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图片.png](http://upload-images.jianshu.io/upload_images/977602-648e373ad94832a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![图片.png](http://upload-images.jianshu.io/upload_images/977602-5f53017e0fe2ddbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
打开Host文件后，添加本文下方附件的内容到host文件中即可，记得保存。
其他用户的host文件位置：

Android用户：首先必须root手机，然后安装root explorer管理器，打开进入/system/etc目录,长按host文件，弹出菜单拉到下面会看到“文本编辑器方式打开”。编辑输入即可

Mac OS用户： host位置为：/private/etc/hosts

iPhone用户：需越狱，使用 iFunBox、PP助手、同步助手、iFile 等访问设备文件系统，备份并修改该文件后覆盖：/etc/hosts

Linux系统hosts位于 /etc/hosts

无需麻烦的翻墙，只要修改host文件，就可以登陆到外国有名的网站google.
一、去浏览器搜索关键词：google hosts 最新。点击首个搜索链接，进入后会有相关的最新google hosts更新文档，将其全部复制。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-ff1244bcac96944e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图片.png](http://upload-images.jianshu.io/upload_images/977602-6e41671975d0da25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
二、再去系统（c盘）盘查找host文件，路径为C:\Windows\System32\drivers\etc\HOSTES。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-16c7b05eab337dab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
三、再用记事本打开host文件，将第一步复制的内容粘贴到host文件的尾部即可。

四、修改后保存host文件，再去浏览器登陆google,即可登陆谷歌官网了！

附：[2017年host文件](http://www.smallkingworld.com/others/hosts.txt)


