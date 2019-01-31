---
title: 解决ThinkPHP部署时Access denied.问题
date: 2017-05-06 11:49:40
categories:
tags:
---
用thinkphp 3.2写微信公众号时遇到的nginx部署问题
<!-- more -->
在本地开发(LAMP)好的ThinkPHP应用，部署到腾讯云时，先是提示_STORAGE_WRITE_ERROR_:./Runtime/Cache/Home/，搜索一下得知是目录权限问题，于是使用命令chmod -R 777 目录名。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-f3f8f7b052048eb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以访问首页后，一跳转到其它页面时，就提示Access denied.

![图片.png](http://upload-images.jianshu.io/upload_images/977602-60a9f2ef922c20b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 于是,发现是ThinkPHP的URL_MODEL问题导致ngnix解析问题。

于是我是这样解决的：
进入服务器，首先改php.ini文件。将cgi.fix_pathinfo的值改成1。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-99f69e13c71904aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后再到nginx.conf中，添加fastcgi_split_path_info
 ^(.+\.php)(/.+)$;

![图片.png](http://upload-images.jianshu.io/upload_images/977602-fccfc8237af165cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后到配置域名解析的文件下(一般是以域名命名的配置文件)。加上这三句
```
fastcgi_split_path_info ^(.+\.php)(/.+)$;  
fastcgi_param   PATH_INFO   $fastcgi_path_info;  
fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;  
```

![图片.png](http://upload-images.jianshu.io/upload_images/977602-58e8a4e1ae3503df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后就解决了。。。

[参考1](http://stackoverflow.com/questions/21909860/access-denied-on-nginx-and-php)
[参考2](http://stackoverflow.com/questions/23390531/access-denied-403-for-php-files-with-nginx-php-fpm)
因为我的Mac上使用的是LAMP套装，所以很多在 Mac 上[测试](http://lib.csdn.net/base/softwaretest)好的，放到云端就有各种问题。主要是Nginx和Apache的主要问题，毕竟Apache比Nginx的权限更大。所以我就装我云上的Web服务器更换成了Apache。

原文地址：http://blog.csdn.net/a787031584/article/details/53400250

