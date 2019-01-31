---
title: 搬瓦工VPS架设VPN教程
date: 2016-11-22 15:27:01
tags:
---
有独立架设VPN用于科学上网的想法很久了，但由于种种惰性一直没有付诸实践，都是在勉强用GoAgent代理浏览器，或者修改hosts重定位IP。用过这两种方式的人都知道，他们不太稳定。动了购买威屁恩的心思，然而几经比较后发现，综合考虑稳定性、流量，租用VPS可能更划算一些，毕竟威屁恩只能用于网络连接，而VPS还可以用于部署网络应用。做了一下前期调研，选择了搬瓦工的VPS，没有别的原因，就是便宜（但不那么稳定，如果求稳请考虑DigitalOcean和Linode）。购买需注册Paypal。
<!-- more -->
原文：http://blog.csdn.net/hjhjw1991/article/details/45848565

0. 前言
有独立架设VPN用于科学上网的想法很久了，但由于种种惰性一直没有付诸实践，都是在勉强用GoAgent代理浏览器，或者修改hosts重定位IP。用过这两种方式的人都知道，他们不太稳定。动了购买威屁恩的心思，然而几经比较后发现，综合考虑稳定性、流量，租用VPS可能更划算一些，毕竟威屁恩只能用于网络连接，而VPS还可以用于部署网络应用。做了一下前期调研，选择了搬瓦工的VPS，没有别的原因，就是便宜（但不那么稳定，如果求稳请考虑DigitalOcean和Linode）。购买需注册Paypal。这不是广告。ps：虽然主要参考了这篇博文，但其间也是踩了一些坑，都写在本文中了。转载请注明出处。
1. 安装系统
在注册购买好vps后，根据以下截图进入kiwivm管理面板：![kiwivm](http://upload-images.jianshu.io/upload_images/977602-c6b42f6f63bb9432.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
面板上默认可能安装的系统是CentOS 6如果不是，想安装系统，从下面进入![os](http://upload-images.jianshu.io/upload_images/977602-49db9fd09ac2b8cf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)选择相应系统就行了。我采用的默认的CentOS 6系统。由于一直是用ubuntu的，对centos的yum还不是很熟悉。之后会写一篇关于在centos6上升级[Python](http://lib.csdn.net/base/python)到2.7的教程，centos6的yum跟python2.6有深度绑定，所以无法直接升级，需要编译源代码来升级。而且安装setuptools也是有坑的。
2. 安装服务（VPN-PPTP）
2.1 登陆系统
安装完系统后登录系统，如果你是Windows系统登录，可使用putty，xshell等登录VPS，也可以直接使用kiwivm管理界面的Root shell - interactive启动[HTML5](http://lib.csdn.net/base/html5)管理交互界面（无需输入密码）。putty登陆方法：选择登陆方式为ssh登陆 ，IP地址和port填写你VPS的IP和port，点open即可。为方便以后登陆，可以起一个session名字并点击save保存配置。putty登陆需要输入账号密码，默认账号是root，密码从以下界面产生![获得随机密码](http://upload-images.jianshu.io/upload_images/977602-339923b43e106982?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
kiwivm管理界面随机产生root密码，这是出于安全考虑。为方便使用，可以以root登陆系统后增加用户，或者更改root的密码更改密码的命令是：
```
passwd
```

按照提示输入两遍新密码即可。
2.2 安装PPTP服务
运行如下命令
```
wget http://www.5yun.org/Soft/linux/Openvz-vpn/openvps_vpn_centos-5-6.sh
chmod a+x openvps_vpn_centos-5-6.sh 
bash openvps_vpn_centos-5-6.sh
#如果以上地址不可用，可尝试以下命令，
#这个脚本只提供三个选项，一般选择1就可以自动完成全部过程
#去掉注释符号
#wget http://www.hi-vps.com/shell/vpn_centos6.sh#chmod a+x vpn_centos6.sh
#bash vpn_centos6.sh
```

上边第一步是获取一个自动脚本，第二步是给它运行权限，第三步是运行。有时候会遇到第一步无法成功，这时候在本地先下载这个文件，再使用Putty或者SSH客户端上传到VPS也是可以的。执行以上命令后将会返回一个选择系统版本的提示信息，因为之前我们选择的是centos6 ，因此选择第2项，输入2，回车：
```
please select your operation system
which do you want to?input the number.
1. my system is centos5 32bit(only support 32bit)
2. my system is centos6 32bit or 64bit(they are support)
3. repaire vpn service
4. add vpn user
```

执行命令后将自动安装，成功后返回一下信息：
vpn service is installed, your vpn username is vpn_name,vpn password is ******** 
这句话提示成功创建了一个名为vpn_name的账户，密码为 ******。

执行命令后报404错误，或者提示文件或目录不存在，是因为没能成功下载安装包。这里提供手动下载安装包的方法如果是centos6，执行以下命令：
```
wget http://linux.dell.com/dkms/permalink/dkms-2.0.17.5-1.noarch.rpm
wget https://acelnmp.googlecode.com/files/kernel_ppp_mppe-1.0.2-3dkms.noarch.rpm
wget https://qiaodahai.googlecode.com/files/pptpd-1.3.4-2.el6.i686.rpm
wget https://logdns.googlecode.com/files/ppp-2.4.5-17.0.rhel6.i686.rpm
```

如果是 centos5，则执行以下命令：
```
wget http://linux.dell.com/dkms/permalink/dkms-2.0.17.5-1.noarch.rpm
wget https://acelnmp.googlecode.com/files/kernel_ppp_mppe-1.0.2-3dkms.noarch.rpm
wget https://acelnmp.googlecode.com/files/pptpd-1.3.4-1.rhel5.1.i386.rpm
wget https://fastlnmp.googlecode.com/files/ppp-2.4.4-9.0.rhel5.i386.rpm
```

4. 添加自己的VPN账号
如何添加自己的VPN账户名？ 比如我想用 anonymous 这个帐号，密码设置为 abc@123 (**注意，危险！仅作为演示用，千万别设置这样的密码！**)
执行下面这句代码来添加威屁恩账户：
```
bash openvps_vpn_centos-5-6.sh
```
返回的信息选项中，选择第4项：
4.add vpn user
根据提示输入用户名，如 anonymous，再输入密码 即可完成VPN的架设了。使用时，在本地新建威屁恩连接，地址和端口填写VPS的地址和端口，用户名密码填写自己设置的威屁恩的用户名和密码，然后连接，就可以了。如有疑问，请留言讨论。

4. reference
搬瓦工(bandwagonhost) 搭建威屁恩 2014.12月测试可行 Centos6.X 威屁恩 pptp 搭建方法 搬瓦工vps 威屁恩架设
5. Appendix
最近妖风太盛，所以如果自己有能力的童鞋，请移步Github的这个项目MPTUN自己研究怎么搭建VPN吧，当然VPS还是必要的。单单这篇文章访问量超高，让我陷入了沉思……

补充一点东西：有能力的同学，可以研究一下ssh的端口流量转发，浏览器或网络连接的socks5代理，基本上可以解决威屁恩被封杀时候的情况。ssh的协议特征很明显，我觉得迟早还是会被解决，同学们如果不嫌麻烦，威屁恩还有几种通信协议，可以研究一下。希望不会被查水表吧，我也不喜欢喝茶。

如果只需要浏览器FQ，而不需要全局FQ，那么SSH代理配合SwitchyOmega插件使用chrome浏览器就可以做到。最近发现一个方便的工具，Chrome插件Secure Shell，在浏览器完成SSH代理。