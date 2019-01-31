---
title: ios打包ipa的四种实用方法(.app转.ipa)
date: 2017-04-14 12:32:03
categories:
tags:
---
iOS打包
<!-- more -->
总结一下，目前.app包转为.ipa包的方法有以下几种：

1、Apple推荐的方式，即实用xcode的archive功能

Xcode菜单栏->Product->Archive->三选一，一般选后两个。

局限性：个人开发一般采用这种方法，但是当一个证书多人使用时就稍显麻烦。一般多人开发时都是采用provisioning profile+P12文件来进行真机调试。上述方法在最后导出ipa包时需要输入appleID，这时还要向团队的其他人要。采用provisioning profile+P12真机调试的方式不要求开发者知道appleID以及密码，密码一般放在leader那里；

 

2、iTunes拖入(推荐)

这种方法十分方便。具体步骤请看动态图：

![itunesApp.gif](http://upload-images.jianshu.io/upload_images/977602-d70976b053b39eaf.gif?imageMogr2/auto-orient/strip)

注意：itunes里的“我的应用程序”是指电脑上的程序，不要求联机，可以把里面的app删除


**3、自动编译脚本**
编写一个全自动编译脚本，从而不用打开XCODE编译运行即可实现打包，这种方法也十分快捷。有兴趣的可以看[这篇文章](http://www.51testing.com/html/04/344504-822334.html)。
缺点：不出错还好，一旦有语法错误或者其他错误出现就不好处理

4、解压改后缀名(本文推荐)

这种方式是在xcode编译产生出.app包的基础上进行进一步处理，通过简单的压缩以及该后缀名即可实现ipa发包。

这种方式下又可通过脚本自动处理以及手动处理两种途径实现，推荐脚本方法，一劳永逸。

 

4.1 脚本自动生成ipa包

Step1: 新建文件夹，命名为“distribute”，新建distribute.sh脚本文件，内容为：(注意，脚本中所有appName请先替换成你的真正app名称)

```
rm -rf appName
mkdir appName
mkdir appName/Payload
cp -r appName.app appName/Payload/appName.app
cp Icon.png appName/iTunesArtwork
cd appName
zip -r appName.ipa Payload iTunesArtwork

exit 0
```

Step2: 将要转化的.app文件放到distribute/文件夹下

这时的文件夹目录结构是这样的：(注意，脚本中所有appName请先替换成你的真正app名称)

```
distribute/distribute.sh
distribute/appName.app
```

Step3: 运行distribute.sh脚本

打开Terminal，cd到distribute文件夹下，把distribute.sh拉到terminal中执行。如果提示permission denied，则用“chmod 777 distribute.sh”命令赋予权限后，再执行一次distribute.sh。

 

Step4: 大约若干秒后，会在distribute/文件夹下生成appName/文件夹，里面的appName.ipa就是我们想要的包。

4.2 手动压缩改后缀方式

这种方式与4.1的方法本质是一样的。

Step1: 新建“Payload”文件夹，注意名字要一字不差；

Step2: 将你的.app包放到Payload中，注意app的名字不做任何更改，就用xcode生成的app名称；

Step3: 在Payload文件夹上右键压缩成zip，然后将生成的.zip文件后缀改成.ipa即可

原文地址：http://www.cnblogs.com/wengzilin/p/4601684.html

