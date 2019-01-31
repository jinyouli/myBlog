---
title: iOS逆向入门实践 — 逆向微信，伪装定位
date: 2017-09-07 17:39:54
categories:
tags: iOS逆向工程
---
#1. 基本知识概述
这次实践的最终目的<!-- more -->，是要实现“自由设定微信定位”的功能，这个功能的操作流程应该是：
1、打开 APP，输入一对经纬度数据
2、进入微信，APP 自动读取输入的经纬度数据，作为使用“附近的人”时的数据来源

#1.1 tweak
而要实现这个功能，很显然并不是通过修改微信的工程源码。达成目的索要采取的手段是tweak。在 iOS 中，tweak
特指动态链接库，用于站在其他优秀 APP 的肩膀上添砖加瓦，来达到某种目的。大部分的 tweak 都是基于 MobileSubstrate 工作的。

1.1.1 tweak 工作原理

在 OC 级别的 tweak 的工作原理主要是使用了 OC 语言的特性(那么对于 swift 编写的 APP 是否有效呢，先卖个关子)。OC 中对某个对象的方法的调用并不像 C++ 一样直接取得方法的实现的偏移值来调用，所以 C++ 方法与实现的关系在编译时就可确定。而 OC 中方法和实现的关系是在运行时决定的。在调用某个对象的方法时，实际上是调用了obj_msgsend向对象发送一个名称为方法名的消息，而我们可以替换这个响应这个消息的实现内容。OC 中比较有力的动态特性Method Swizzing就是建立在这个基础之上。

1.1.2 tweak 编写套路
**a.确定需求**
这是当然的吧，你需要逆向的对象是什么，dylib？bundle？daemon？，想要实现什么样的功能。

**b.定位目标函数**
这里就需要使用后面会介绍到的 class-dump 了。dump 出所有头文件之后，寻找自己感兴趣的函数。实际上为了更好地分析内部实现以实现我们的功能，可能还会用到一些静态分析的工具。

**c.编写 tweak**
知道了需要 hook 的对象以及方法之后，我们就可以开始编写 tweak 了。编写 tweak 可以使用 Theos 或者是 iOSOpendev，这两者的关系就像是 git 跟 SourceTree 的关系一样。

1.2 MobieSubstrate
MobieSubstrate 是现有越狱插件运行的基础。它由3部分组成：
**MobileHooker**
它的作用是替换函数实现，也就是所谓的 hook 技术。比如大名鼎鼎的 Theos 就是基于这个 hooker实现的。

**MobileLoader**
它的作用是加载第三方动态链接库，也就是我们开发的 tweak。在 iOS 启动时，由 launchd 将 MobileLoader 载入内存，然后 MobileLoader 会 dlopen 所有/Library/MobileSubstrate/DynamicLibraries/
目录下的动态链接库。另外我们需要为编写的 tweak 同时编写一个跟 tweak 同名的 plist 文件，指定 tweak 的作用范围。

**Safe Mode**
bug 是不可避免的，如果寄生的 tweak 出现了 bug 导致 APP 崩溃，那么我们就需要一种机制，将 tweak 禁用掉，跟我们一个机会来 debug。而 Safe Mode 会捕获 SIGTRAP, SIGABRT, SIGILL, SIGBUS, SIGSEGV, SIGSYS 这6种信号，然后进入安全模式。

在 iOS9.3 越狱时 MobieSubstrate 已经自动安装上了。目前在 Cydia 中也更名为了 Cydia Substrate。

#2. 砸壳
所谓砸壳，就是对 ipa 文件进行解密。因为在上传到 AppStore 之后，AppStore自动给所有的 ipa 进行了加密处理。而对于加密后的文件，直接使用 class-dump 是得不到什么东西的，或者是空文件，或者是一堆加密后的方法/类名。

##2.1 使用 Clutch
砸壳可以使用工具[Clutch](https://github.com/KJCracks/Clutch)曾经在 Cydia 中可以直接下载安装 Clutch， 现在已经不行了，需要自己编译然后 scp 到真机上。下面开始来一步一步配置 Clutch：在安装了 Xcode 之后(都看到这篇文章了没理由没安装吧)，安装另外的 Command line tools：
```
xcode-select --install
```
接着将 Clutch clone 到本地：
```
git clone git@github.com:KJCracks/Clutch.git
cd Clutch
```
Build：
```
xcodebuild -project Clutch.xcodeproj -configuration Release ARCHS="armv7 armv7s arm64" build
```
Build 完之后会在./Clutch
目录下生成可执行文件Clutch
，查看一下文件的权限，其他用户是否可执行：
```
ls -l -h -p ./Clutch/clutch

-rwxr-xr-x 1 Pandara staff 1.1M 8 10 21:45 clutch
```
将它 scp 到越狱机器上。这里需要越狱机器已经安装了 openSSh，并且建议同时安装 Mobile Terminal，更改你的默认密码：
1、输入命令su root
2、输入默认密码 alpine
3、输入passwd root

来更换你的 root 密码拷贝文件到你的机器上：
```
scp clutch root@<DeviceIP>:/usr/bin/
```
接着 ssh 到你的机器上，然后使用 clutch 列出所有已安装的 APP：
```
clutch -i
```
根据编号或者 bundleID 来执行 dump
```
clutch -d com.tencent.xin
```
结果得到了错误信息，说当前版本的 clutch 不支持 dump watchOS2 的 APP…
```
com.tencent.xin contains watchOS 2 compatible application. It’s not possible to dump watchOS 2 apps with Clutch 2.0.3 at this moment.
```
只好尝试用 dumpdecrypted.dylib 来砸壳

##2.2 使用 Dumpdecrypted
在 make dumpdecrypted 时收到几个 warning，都是说找不到XXX/PrivateFrameworks
这个目录。直觉告诉我不解决这几个 warning 会导致以后一些奇奇怪怪的问题。Google 之后发现，出现这个问题是因为 Xcode 7.3(ios sdk 9.3) 移除了所有 private framework。但是我们可以下载 ios sdk 9.2 然后解压到相应目录，并且将 Makefile 里面的 SDK 路径修改为下载下来的 9.2sdk。9.2sdk可以在[这里](https://sdks.website)下载到，修改 Makefile 中的 SDK 为实际的 9.2sdk 路径：
```
#SDK=`xcrun --sdk iphoneos --show-sdk-path`
SDK=/Users/Pandara/Downloads/iPhoneOS9.2.sdk
```
之后再 make 便可以成功生成 .dylib 文件了。接下来的步骤就是要将这个dumpdecrypted.dylib
文件 copy 到 APP 的沙盒目录的 Documents 目录下。我们知道所有 APP 的沙盒路径都在/var/mobile/Container/Data/Application/XXXXX/
里面，但是我们不知道 XXXXX 到底是哪一个 APP。尝试过多种方法失败之后(可惜 Cycript 不支持 iOS9.3.X 啊！)，推荐使用 iTools 之类的工具拷贝，比较直观，需要先安装 afc2add 补丁。接着开始砸壳：
```
DYLD_INSERT_LIBRARIES=/path/to/dumpdecrypted.dylib /path/to/executable
```
executalbe 的路径可以用下面的命令来查找：
```
ps aux | grep WeChat
```
在当前目录下就会生成 WeChat.decrypted, 这个就是砸过壳的微信了。把它 scp 回来本地之后，就可以开始接下来的操作了。
```
DYLD_INSERT_LIBRARIES
是 Mac 中的环境变量(Linux 上对应的是 LD_PRELOAD)。在这个宏中写入动态库完整路径，就可以在执行文件加载时将该动态库插入。
另外，其实操作完成之后发现，dumpdecrypted.dylib
放在哪里都行，不知道为何参考资料的作者一定要将它放在 WeChat 的 Documents 目录。
```

##3. Class Dump
Class Dump 是为了导出微信的头文件，能够让我们得以浏览感兴趣的类中暴露出来哪些方法，找到需要 hook 的方法名，后面编写 tweak 会需要使用到。
```
class-dump -H WeChat.decrypted --arch arm64 -o output/
```
命令执行完成之后，会在当前目录下生成一个 output 文件夹，里面有导出来的所有微信头文件，包括使用到的第三方 sdk。可以将所有这些头文件放到一个空工程里面方便查看。一共 7000+ 个头文件，导入需要一定时间…[![image](http://upload-images.jianshu.io/upload_images/977602-5fa6b55c55ae89e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/2016/08/11/wechat_fake_location/class_dump.png)
凭借参考文章中作者的提示，MicroMessengerAppDelegate.h
就是微信的 AppDelegate，可以大概看到微信的项目结构。

之所以可以使用 class-dump 来还原类的 @interface 部分，还是多亏了 iOS 采用的文件格式 Mach-O 中包含了足够多的 metadata。

再来看看我们需要实现的功能，是改变我们的位置从而改变附近的人，但是手动猜测类名关键字搜索始终是很低效的行为。其实除了排除法和一个一个推测之外，还可以使用 Reveal 这个工具来帮助我们定位。

##4. 反编译静态分析
class-dump 帮助我们列出了所有 header 文件，让我们对项目结构大体有个认识。不过，我们更加好奇的，始终是 .m 文件里面的实现。这里使用了 Hopper Disassembler 这个工具。注意，导入的文件应该是砸壳之后的文件。否则你看到的是一堆乱码，犹如一开始那傻逼的我…
保险起见，可以先确认一下 .decrypted 是否已经解密了。首先查看一下对应的二进制文件包含哪些架构：
```
> file WeChat.decrypted
WeChat.decrypted: Mach-O 64-bit executable
```
可以看到，砸壳后的二进制文件只包含砸壳时使用的机器的架构，可能是由于使用了 bitCode？接下来使用 otool 来输出 app 的 load commands，查看 cryptid 这个标志位来判断 app 是否被加密了，1代表加密了，0代表被解密了。otool 是 Xcode tool chain 的一部分，所以并不需要额外安装：
```
otool -l WeChat.decrypted | grep -B 2 crypt
```
输出：
```
WeChat.decrypted:
--
cmd LC_ENCRYPTION_INFO_64
cmdsize 24
cryptoff 16384
cryptsize 45383680
cryptid 0
```
可以看到确实是被解密了。丢进去 hopper 之后，可以看到美丽的画面：[![image](http://upload-images.jianshu.io/upload_images/977602-a9b5dfdec60d23c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/2016/08/11/wechat_fake_location/hopper.png)
由于 IDA 暂时不能支持64位架构的包，而 hopper 试图在生成伪代码的时候也提示不能支持包对应的 cpu，所以这里建议，其实可以在 iTools 或者 pp助手 之类的第三方分发渠道下载微信，一般都是已经脱壳了的。然后就可以直接反编译包里面的 armv7s 架构。

##5. tweak 小试
这里暂时先不进行我们的目标 tweak 开始，先来一个小样练练手：实现微信一打开就弹出 Hello World 对话框的功能。

###5.1 创建 Theos 工程
创建 Theos 工程在 Terminal 中完成：
1) 更改工作目录到常用的 iOS 工程目录，然后使用下面命令启动 NIC(New Instance Creator):
```
$THEOS/bin/nic.pl
```
[![64464theos_template.png](http://upload-images.jianshu.io/upload_images/977602-d41286278889335b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/64464theos_template.png)上图中第11种模板就是我们需要使用的

2) 这时候 Theos 需要我们输入工程的名字，以及 deb 包的名字，类似于 bundle identifier:[![32149project_package_name.png](http://upload-images.jianshu.io/upload_images/977602-3c856222b8ec8649.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/32149project_package_name.png)
3) 输入作者名称之后，Theos 要求我们输入 MobileSubstrate Bundle filter，也就是 tweak 作用对象的 bundle identifier，这里我们填上微信的 id，可以在解压后的 ipa 包中的 plist 文件中找到：[![14652author_bundle_id.png](http://upload-images.jianshu.io/upload_images/977602-061d8bcd4ef8a663.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/14652author_bundle_id.png)
4) 最后我们需要输入指定 tweak 安装完成之后需要重启的应用，以 bundle identifier 来表示，这里还是填上微信：[![97994restartid.png](http://upload-images.jianshu.io/upload_images/977602-4ae4bde1c8aca699.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/97994restartid.png)到这里工程便创建完成了，会在当前目录下生成工程文件夹。

5.2 定制工程文件
工程目录中只有4个文件，但是这4个文件是构成我们的 tweak 的4根顶梁柱[![62033files.png](http://upload-images.jianshu.io/upload_images/977602-c2d6fc25cd09bf7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/62033files.png)

1) control
control 文件记录了 deb 包管理系统所需要的基本信息，会被打包到最后生成的 deb 包中。一般无需修改里面的内容。

2) Project_Name.plist
这个跟工程名同名的 plist 文件跟 APP 中的 Info.plist 左右类似，记录一些配置信息，描述了 tweak 的作用范围。[![image](http://upload-images.jianshu.io/upload_images/977602-c74be60fd608b25f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/2016/08/11/wechat_fake_location/plist.png)其中 Bundles 字段下的数组指定了若干个 bundle identifier 作为 twewak 的作用对象。默认是 spring board，这里我们替换成微信的 bundle identifier：com.tencent.xin。可以在解压后的 ipa 包中的 plist 文件里找到。

除了使用 Bundle 指定，也支持使用另外两种指定方式：
**Class:** 对列表中指定的类起作用
**Executables:** 对指定的可执行文件名起作用

这三种配置方式可以同时指定，但是这里有个小问题。在同时指定的时候，一个文件只有满足每种 Filter 中的 array 里的至少一个条件，tweak 才能生效。如果想要满足任意一种 Filter 下的任意一个条件就能生效的话，就必须添加一个键值对：Mode: Any

3) Makefile
Makefile 文件制定编译和链接所涉及的文件、框架、库等信息，将整个过程自动化。需要 Makefile 文件，是因为 theos 就是一个跨铭泰的 Makefile 系统。工程中的 Makefile 内容如下：[![93682makefile.png](http://upload-images.jianshu.io/upload_images/977602-404c78268a694e50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/93682makefile.png)自动生成的内容一般无需更改，除非引入了其他第三方库，或者添加了其他的源码文件，则需要增加引入的新文件。

4) Tweak.xm
Theos 创建工程之后，默认生成的模板源文件是 Tweak.xm，源文件中包含了基本的 Logos 语法的说明，具体写法就不展开了，书中很详细。其中有一些预处理命令值得注意：

###%hook: 
指定需要 hook 的 class

###%log: 
这个指令在 %hook 内部使用，可以将 log 的内容写入 syslog(/var/log/syslog)。这种是将 log 写入文件的方法。在 iOS9.3 这个文件没有自动生成，如果需要以这种方式查看 log，则需要配置一下，配置方法可以参考 [wiki 中的 1.3 内容](https://www.theiphonewiki.com/wiki/System_Log)。而我使用的是 1.1 的方法，利用 socat 这个命令行工具，可以在 cydia 中安装，按照 wiki 的方法实时查看 log

###%orig: 
在 %hook 内部使用，执行被 hook 的函数的原始代码。还可以利用它以 C++ 函数的调用方式改变原始函数传入的参数

###%group: 
用于将 %hook 分组，与 %init 配合使用，在按条件初始化 hook 代码时非常有用

###%ctor: 
用于系统自动初始化默认的未分组 hook 代码

###%new: 
%hook内部使用，用于给现有的 class 添加新的方法

###%c: 
%hook内部使用，动态获取一个类的定义

关于详细的 logo 语法，可以到[这里](http://iphonedevwiki.net/index.php/Logos)一探究竟

###5.3 编写 tweak 源码
要在微信运行之后弹出一个对话框，hook 点应该是 Appdelegate 的 didFinishLaunch 方法。前面在 class-dump 的时候已经猜测到了微信的 Appdelegate 对应类名为MicroMessengerAppDelegate，头文件中也暴露了这个方法出来，因此可以推断出 hook point，编写 .xm 文件如下：[![5187xm.png](http://upload-images.jianshu.io/upload_images/977602-30e891162a9fd26c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/5187xm.png)
%hook指定了需要 hook 的 class，然后编写弹窗代码，在最后 return %orig 调用原始代码。代码中使用了消除警告的宏，这是因为如果出现警告的话，后面 make 编译的时候将无法通过。

###5.4 编辑 MakeFile
[![40438make_file.png](http://upload-images.jianshu.io/upload_images/977602-c2dfb111f4cbbd54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/40438make_file.png)
**THEOS_DEVICE_IP: **指定了目标机器的 ip 地址，因为我接下来采用命令行的方法将包安装到机器上。这需要用到简单的 ssh 命令，所以需要越狱的机器安装 OpenSSH。

**ARCHS: **指定了开发的架构，也就是这个包需要支持的架构。因为我的机器是 iPad Mini 4，所以只需要指定它使用的架构的就可以了。

**TARGET: **指定 tweak 应该工作在哪个 SDK 版本下。这里需要参照目标机器的系统版本，毕竟不用的系统版本可用的 SDK 也不一样。

**wechat_hook_test_FRAMEWORKS: **指定需要导入的 framework，由于使用到了 AlertView，所以这里需要导入 UIKit。另外值得一提，如果需要导入 private framework 的话可以使用XXX_PRIVATE_FRAMEWORK，但是 iOS SDK 9.3 已经去除了 private frameworks，最后具有 private frameworks 的 sdk 版本是 9.2，使用 sdk9.2 编译打包的 tweak 不知道能否工作在 iOS 9.3 中，待验证。

###5.5 编译
走到这步，就说明我们已经看到摸着逆向之路的门框了。想想都有点小激动。
Theos 采用与 debian 相同的 make 命令来编译。执行 make 命令：[![22554make_cme.png](http://upload-images.jianshu.io/upload_images/977602-c1dba1d55c20d8a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/22554make_cme.png)
从输出来的信息可以看到，Theos 完成了预处理，编译，链接，签名等一系列动作。编译完成之后会发现当前目录的 .theos 目录中多出了若干个 .dylib 文件，这就是 tweak 的核心部分。[![59928dylibs.png](http://upload-images.jianshu.io/upload_images/977602-27f36aab1a00cb7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/59928dylibs.png)

###5.6 打包
打包使用的 make package 命令来自 Theos 本身，其实就是先 make 然后再 dpkg-deb，运行时报了一个错误跟一个警告：[![76696make_package_error.png](http://upload-images.jianshu.io/upload_images/977602-eece9418a3168a04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/76696make_package_error.png)
错误指的是，package name 只能包含小写的字母还有数字…于是只好将所有 wechat_hook_test 都替换成 wechathooktest…包括 control, Makefile 还有 plist 文件名
关于警告，在 github 看到解释，大意上是说可以不用管这个 warning，看得不太懂，希望看懂的朋友告诉一声，[链接在此](https://github.com/theos/theos/commit/bb5626b03dceda0a364914ed20fdf8f0118b1f1f)

再次 make package，得到：[![8664make_package.png](http://upload-images.jianshu.io/upload_images/977602-4d5eeb520811fba2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/8664make_package.png)
可以看到在 packages 文件夹中生成了我们需要的包。我这里有两个，是因为我打包了两次。

###5.7 安装
最后，要将这个 deb 文件安装到 iOS 中去。可以用图形化的方法，将 deb 包丢到机器里面，然后用 iFile 来安装。我这里采用了命令行的安装方法。为了简化步骤，让进程使用 ssh 时无需输入密码，这里先做一些操作，就是将我们的 rsa 公钥拷贝到越狱机器上：
1) 先 ssh 到设备上，以 root 身份登录 iOS 设备，输入ssh-key gen，会生成/var/root/.ssh目录，并且里面有一对秘钥
2) exit 回本机，将本机的公钥 cp 到用户目录中，并且更名为 authorized_keys，然后将它 scp 到/var/root/.ssh中
3) 再尝试 ssh 登录到 iOS 设备，应该不用输密码了
接着调用make package install
命令完成编译打包安装一条龙服务，报了一个错误，说找不到进程：[![41504install_error.png](http://upload-images.jianshu.io/upload_images/977602-987182f4a1a80057.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/41504install_error.png)
这个错误是因为我误将 Makefile 中需要 kill 的进程名称填成了 bundle identifier，将它改成 WeChat 就好了：killall -9 WeChat

再次运行，跑通！效果图：[![6855IMG_0003.PNG](http://upload-images.jianshu.io/upload_images/977602-c99f256362e97821.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://pandarazone.qiniudn.com/6855IMG_0003.PNG)


参考资料
1.[ 手把手教你制作一款iOS越狱App](https://github.com/jackrex/FakeWeChatLoc)
2.[《iOS逆向工程学习笔记》](http://blog.csdn.net/jymn_chen/article/details/38361161)
3.《iOS应用逆向工程》，机械工业出版社

原文：http://pandara.xyz/2016/08/13/fake_wechat_location/


