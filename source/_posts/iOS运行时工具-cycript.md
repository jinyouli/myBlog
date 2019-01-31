---
title: iOS运行时工具-cycript
date: 2017-09-05 15:22:06
categories:
tags: iOS逆向工程
---
cycript是大神saurik开发的一个非常强大的工具，<!-- more -->可以让开发者在命令行下和应用交互，在运行时查看和修改应用。它确实可以帮助你破解一些应用，但我觉得这个工具主要还是用来学习其他应用的设计（主要是UI的设计及实现）。
      
这个工具使用了[Objective-C](http://lib.csdn.net/base/objective-c)和[JavaScript](http://lib.csdn.net/base/javascript)的混合模式，可以实时的和应用交互甚至修改应用。它的网址请猛戳这里。在官网上可以下载到完整的软件包。使用的方式有两种，一种是在越狱的设备上通过MobileSubstrate加装，这样可以在所有的应用里使用；另一种是通过静态库的方式把cycript集成到自己的应用，这样做不要求越狱，当然也只能在自己的应用内使用了。
      
在越狱模式下cycript的安装：
      1. 在cydia下安装openSSH，这样可以确保能用SSH登录到[iOS](http://lib.csdn.net/base/ios)设备上，如果你已经安装过了，可以不用继续安装了
      2. 用sftp上传下载的cycript_0.9.501_iphoneos-arm.deb和libffi_1-3.0.10-5_iphoneos-arm.deb安装包到[ios](http://lib.csdn.net/base/ios)设备上
      ![](http://upload-images.jianshu.io/upload_images/977602-45ab86633433e447?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
      3. 用dpkg -i来安装deb包
      ![](http://upload-images.jianshu.io/upload_images/977602-6c46daaba90edbe6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
      4. 运行cycript，如果出现cy#的符号，那么就是安装完成了
      ![](http://upload-images.jianshu.io/upload_images/977602-0220c5772b7e8739?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
      安装之后自然是使用，这个使用方法网上讲得比较详细了，很多拿的还是支付宝的例子，所以在这里顺便提醒一下小伙伴们，现在设备集成了越来越多的应用，重要性和不可替代性都是越来越高，所以设备最好还是不要越狱，安全第一嘛。
      
cycript的用法上主要是注入你关注的那个应用的线程，然后就可以获得app，获得window，慢慢去获得viewController，逐步逐步拨开UI的面纱，这个在学习经典应用的UI时真的是无上的利器！
      
下图是我在跟踪[微信](http://lib.csdn.net/base/wechat)的UI时的样子，大致上方向就是这样逐步深入。
      ![](http://upload-images.jianshu.io/upload_images/977602-7665cc910e7d26af?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

      
上面的例子是在越狱的机器上安装cycript，然后可以在任意的应用中使用。
      
还有一种用法是在开发过程中，把cycript的framework集成在应用中，这样可以用于实时调整UI的参数，而且不要求机器越狱。下面给出一个最最简单的例子：
      
1. 从官网下载cycript的包，是一个压缩文件，里面包括三个cycript.framework，cycript.lib和cycript
     
2. xcode里面新建一个target，仅仅用最简单的singleViewApplication创建一个空白的应用，这时界面应该是一片纯洁
      
3. 添加cycript框架以及依赖，添加cycript.framework框架是应有之义，但这个框架还需要依赖库的支持，包括JavaScriptCore和libstdc++；这里需要注意的是libstdc++是有版本要求的，必须是libstdc++.6.0.9.dylib，如下图所示
      ![](http://upload-images.jianshu.io/upload_images/977602-2146b059a1bfdef5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
      
4. 设置编译选项
          为了解决libstdc++的兼容问题，还需要在BuildSetting页设置"Other Linker Flags"，添加-lstdc++；另外还有“C++ Standard Library”，确保选择了“Compiler Default”。如果没有选择特定的libstdc++版本并且正确配置编译器选项的花，在iOS7下链接会失败的，这一点请务必注意。
      
5. 修改代码，打开cycript监听端口
          这个最好用一个宏来包一下，比如用：CYCRIPT_ENABLE
```
#ifdef CYCRIPT_ENABLE  
    CYListenServer(8888);  
#endif  
```
          
这里的8888就是cycript的监听端口，为了让这句代码起作用，请把CYCRIPT_ENABLE在加入到预设宏里面。
      
6. 运行模拟器，这里还有一个要说明，目前只支持64bit的，不能使用32bit的模拟器，这个也需要配置一下，然后选择正确的模拟器运行，应用就可以跑起来了，仍然是一片纯洁的UI
      
7. 进入cmd界面，切换路径到cycript包的解压目录下，运行./cycript -r 127.0.0.1:8888
          
其中，127.0.0.1是你的模拟器或者设备地址，8888就是你代码里面配置的监听接口，如果正常，会进入cycript的REPL，这时就可以现场修改一些UI了，比如把白色背景改成红色：
      ![](http://upload-images.jianshu.io/upload_images/977602-efce853d10b3cbee?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
      此时的UI应该就变成了红色背景。
      ![](http://upload-images.jianshu.io/upload_images/977602-daf0320e48f2f22a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
      
      
cycript的使用基本方法就是这样了，剩下的就是如何使用的问题了，这个一方面需要对iOS的框架有足够的了解，另一方面也需要积累经验。

原文地址：http://blog.csdn.net/sakulafly/article/details/29633627

