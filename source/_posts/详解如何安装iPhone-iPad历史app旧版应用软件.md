---
title: 详解如何安装iPhone/iPad历史app旧版应用软件
date: 2017-02-15 14:56:57
categories:
tags:
---
未越狱的iOS应用软件只能升级不能降，有时app升级后却不如原来的版本用的顺手。本教程中详细介绍了如何在未越狱的iOS设备上安装旧版本app的方法，小编力图写的详细并兼顾明了，希望可以达到“仔细瞅几眼就会”的效果。
<!-- more -->

   未越狱的iOS应用软件只能升级不能降，有时app升级后却不如原来的版本用的顺手。本教程中详细介绍了如何在未越狱的iOS设备上安装旧版本app的方法，小编力图写的详细并兼顾明了，希望可以达到“仔细瞅几眼就会”的效果。

工具/原料
    iOS设备
    windows电脑，Fiddler（软件名）、itunes（不做解释）、itools（苹果助手软件）

一、装备工作
1、关于Fiddler是啥，干啥的，怎么下载 自行百度。小编直接下载如图第一个检索结果的软件，Fiddler4。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-731ec7003ed7298b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

二、抓包下载旧版app
1、安装好Fiddler后在【开始】菜单里打开。
执行Fiddler options——HTTPS——在如图框中粘贴 gsa.apple.com （设置白名单gsa.apple.com）——Actions——Trust root certificate

![图片.png](http://upload-images.jianshu.io/upload_images/977602-cb5996c72e1d899e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2、在itunes登录你的AppleID，检索要下载的app。
然后造Fidder左下角如图的黑框中粘贴 bpu MZBuy.woa/wa/buyProduct ，键盘敲回车。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-a9ea865117e0fba4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3、在itunes的检索页面直接点击应用下的获取按钮，你会发现下载并未开始，因为经过上步设置，Fidder拦截了。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-5a4f41dbc68f3d9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4、如图，在fidder左侧找到红色T字母图标的链接，选中；右侧Inspectors——Textview——使用旧版本app的ID替换现在的要下载的最新版本app的ID（关于ID如何获取请自行百度或者参考本人后续教程）——Run to completion.
[如何获取ios历史App应用软件的ID号
](http://jingyan.baidu.com/article/7908e85ca24a29af481ad2ef.html)

![图片.png](http://upload-images.jianshu.io/upload_images/977602-57216b8b5d57ac07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5、回到itunes发现app开始下载，这时下载的便是旧版本app了。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-ce9188bd7a034576.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 三、安装

下载完成后ipa格式安装包存放在C:\Users\Administrator\Music\iTunes\iTunes Media\Mobile Applications 路径下。

如果修改过存储路径，可在itunes的“偏好设置”中查看。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-b97924fdc35dca46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2、将ios设备连至电脑，使用itools安装app即可。
另外，[不让iphone/ipad应用软件app不提示升级参考链接教程](http://jingyan.baidu.com/article/6dad50751e0411a123e36efb.html)


![图片.png](http://upload-images.jianshu.io/upload_images/977602-3624a23741e61d84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

