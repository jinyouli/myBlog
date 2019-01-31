---
title: VPS+LNMP+WordPress搭建个人网站/博客
date: 2017-05-06 12:07:20
categories:
tags:
---
wordPress搭建个人博客
<!--more-->
恭喜你找到这里，自认为这篇建站教程还是不错的，看完后你应该能够搭建出一个网站来。

但下面的链接是一篇更好的文章，我推荐你去也那里看看。
[VPS搭建LAMP安装WordPress建站及优化教程Vultr & 搬瓦工VPS亲测](http://www.seoimo.com/wordpress-vps/)

写在前面
很久之前就想拥有一个属于自己网站，和腾讯新浪等提供的公共空间不同，这是属于自己的地盘，是自己在互联网上的一亩三分地，可以想怎么玩就怎么玩（尽管服务器域名程序都是别人的）。

想象中做出一个网站难度是很大的，实际操作后才知道现在网站建设的各个方面都已经非常成熟，即使什么都不会，搜搜教程，很快就能折腾出来一个网站。基本零基础的我花了一天时间就从零进行到能打开网页的程度，其中大部分时间还是在选择VPS和网站程序（WP or Z-blog）。

.
有了这样一个网站，或者说是博客，我能干什么呢？

首先自己有一些东西可以在这里分享，教程、软件之类的。分享是一种快乐，安利是一种美德。
自己平时也会写一些东西，现在都是写在有道云笔记里自己看，以后也可以尝试在这个网站里面记录。**所以这也决定了这个网站的定位，不是像很多访问量很大的个人网站那样以技术文章为主，这是我自己的空间，会加入我自己的生活。**
最后还可能会放一些专业性的东西在里面，或许会和以后的工作有关。

既然网站已经搭建完成，那么第二篇文章当然要写怎么搭建网站。网上很多教程，自己多方综合，应该还是算比较完善的。那么，开始吧！
1 购买域名和VPS
我们访问网站的时候输入的是一个地址，而网站是放在远程的服务器上，这也就是搭建个人网站最基本的需要掏钱的地方：域名、服务器。
1.1 购买域名
域名推荐上阿里的[万网](http://wanwang.aliyun.com/)购买。输入想要的域名，查询，不同的后缀有不同的价格，选择想要的域名购买就行。**这里推荐选好域名后把相应的各种帐号都注册了，邮箱、百度、微信等，就算不用也能起一个保护作用。**


![图片.png](http://upload-images.jianshu.io/upload_images/977602-53a3702bcc3c15e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1.2 购买服务器
接下来考虑搭建网站的服务器。这里有两个选择，虚拟主机或者VPS。虚拟主机已经配置好网站运行环境，但是你只能在那个环境下玩；而VPS就是一个服务器，有自己的cpu内存等，可以自己装系统，灵活性可玩性更大。我们这里选择VPS。

关于VPS的选择，有很多帖子可以参考，比如知乎这个问题[有哪些便宜稳定，速度也不错的Linux VPS 推荐？](https://www.zhihu.com/question/20800554)，我也是在这个的推荐下选择了bandwagonhost的VPS。

bandwagonhost一年20刀的VPS配置为：
```


    Location: Fremont CA (no other locations available on this plan) 

    SSD: 10 GB 

    RAM: 512 MB 

    CPU: 1x Intel Xeon 

    BW: 1000 GB/mo 

    Link speed: 1 Gigabit VPS 

    technology: OpenVZ/KiwiVM 

    Linux OS: 32-bit and 64-bit Centos, Debian, Ubuntu, Fedora

    1 Dedicated IPv4 address 

    Full root access

```
[购买地址](https://bandwagonhost.com/cart.php)
下面关于bandwagonhost VPS的说明都是参考自[搬瓦工vps](http://banwagongvpn.lofter.com/)。

从上面的购买地址进入bandwagonhost，选择按年付费，$19.99，不放心的可以先买一个月试试，$2.99。我就是先买一个月试了一下，感觉连接速度还不错，打算长期使用。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-d8600c09ecb69407.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Add to Cart→Checkout，然后会弹出一个相当于注册的界面，用拼音如实填写即可。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-159a3343792c9fa8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
最后付款选择PayPal或者支付宝，我这里是用PayPal。PayPal只要有储蓄卡就能用付款，去PayPal官网注册，绑定储蓄卡即可。具体的支付界面这里就不贴出了，主要是我暂时没有付款的需求。

好了买下后去VPS后台看看。Services→My Services→KiwiVM Control Panel

注意如果先买的一个月，后来要把账单周期改为一年，也在My Services中更改。Billing: Annually [modify]，下面这张图上暂时没有出现，后来就有这个选项了。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-3eaa4cc58d53b526.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后就进入如下的界面，可以看到自己VPS的IP。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-c35bf81e8ba1d401.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这里也没有过多需要说的，因为摸索两分钟就都能明白是什么了。

左边的Main controls显示主界面，包括运行状态，开关机重启等操作；Detailed statistics可以监控运行状态；Install new OS可以安装新的系统；Shadowsocks Server一键安装SS，可用于爬墙，简直人性化。

这里说明一下Status:LA: 0.00/0.00/0.00，这是Linux显示负载的方式，分别是1分钟、5分钟、15分钟内系统的平均负荷，1代表满载，可能出现超过1的情况。
1.3 域名解析

好了，域名有了，VPS也有了，可以进行域名解析了（把域名指向网站空间IP）。

进入阿里云的管理控制台，点击域名菜单，在自己的域名后面点击解析。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-809b8e666786ca4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如下图设置，IP填写VPS的IP，主机记录填www代表将域名解析为www.jwcyber.com，填写@代表将域名解析为jwcyber.com，两个都写上吧，后面我们会用301重定向让它们都能使用。解析需要等待一段时间才会生效，我们先开始搭建网站。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-9b357ede183bbfdf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2 安装网站运行环境LNMP
2.1 WordPress介绍+VPS安装系统

我这里使用WordPress来搭建网站。WordPress是一种使用PHP语言开发的博客平台，用户可以在支持PHP和MySQL数据库的服务器上架设属于自己的网站。也可以把 WordPress当作一个内容管理系统（CMS）来使用。WordPress有许多第三方开发的免费模板，还有成千上万个各式插件，安装方式简单易用。

不管怎样，只要知道WP国内外用的人很多就行，经过大家检验的肯定不错。（用了后才知道不管什么问题都能搜到解决方案。）
下面的步骤都是依据[搬瓦工VPS安装WordPress详细图文教程](http://www.banwagong.com/213.html)

首先在Main controls中stop，然后Install new OS，选择centos-6-x86，Reload。

在接下来的界面中记住 root password 和 SSH Port！

![图片.png](http://upload-images.jianshu.io/upload_images/977602-d2dff65127fe86bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
安装好centos系统以后，就可以通过SSH连接VPS安装网站环境了。这里需要使用一个软件：[putty](http://www.chiark.greenend.org.uk/%7Esgtatham/putty/download.html)。
运行putty，输入IP和SSH Port，Open。这里可以save一下，方便以后使用。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-2221d1385bea9d64.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
进入的界面后，login as: root，回车；需要password，复制之前保存的密码，右键粘贴，回车就可登录VPS。（putty中鼠标右键为粘贴。）

![图片.png](http://upload-images.jianshu.io/upload_images/977602-94a09b7b5be22654.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2.2 安装LNMP环境
putty登录VPS后就可以安装网站的环境了，这里使用LNMP一键安装包，详细查看[LNMP官网](http://www.lnmp.org/)。
LNMP一键安装包是一个用Linux Shell编写的可以为CentOS/RadHat/Fedora、Debian/Ubuntu/Raspbian VPS(VDS)或独立主机安装LNMP(Nginx/MySQL/PHP)生产环境的Shell程序。WordPress就依靠这些环境运行。下面的步骤我直接粘贴[LNMP官网的教程](http://lnmp.org/install.html)。
**安装步骤:**
2.2.1 screen
使用putty或类似的SSH工具登陆VPS或服务器；登陆后运行：screen -S lnmp
如果提示screen: command not found 命令不存在可以执行：yum install screen 或 apt-get install screen安装，详细的[screen教程](http://www.vpser.net/manage/run-screen-lnmp.html)。
2.2.2 下载并安装LNMP一键安装包

您可以选择使用下载版(推荐国外或者美国VPS使用)或者完整版(推荐国内VPS使用)，两者没什么区别，只是完整版把一些需要的源码文件预先放到安装包里。安装LNMP执行：

    wget -c http://soft.vpser.net/lnmp/lnmp1.2-full.tar.gz && tar zxf lnmp1.2-full.tar.gz && cd lnmp1.2-full && ./install.sh lnmp

按上述命令执行后，会出现如下提示：
![1456998176878130.png](http://upload-images.jianshu.io/upload_images/977602-c74bc649dceadd9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
需要设置MySQL的root密码（不输入直接回车将会设置为root），输入后回车进入下一步，如下图所示：
![1456998215359356.png](http://upload-images.jianshu.io/upload_images/977602-5c0da65e8479a274.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这里需要确认是否启用MySQL InnoDB，如果不确定是否启用可以输入 y ，输入 y 表示启用，输入 n 表示不启用。默认为y 启用，输入后回车进入下一步，选择MySQL版本：
![1456998243132973.png](http://upload-images.jianshu.io/upload_images/977602-f926b430a54cdbb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
输入MySQL或MariaDB版本的序号，回车进入下一步，选择PHP版本：
![1456998291652362.png](http://upload-images.jianshu.io/upload_images/977602-fac54c4de050b630.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
输入PHP版本的序号，回车进入下一步，选择是否安装内存优化：
![1456998340597837.png](http://upload-images.jianshu.io/upload_images/977602-0bf1dac015a3d56e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以选择不安装、Jemalloc或TCmalloc，输入对应序号回车。
提示"Press any key to install…or Press Ctrl+c to cancel"后，按回车键确认开始安装。LNMP脚本就会自动安装编译Nginx、MySQL、PHP、phpMyAdmin、Zend Optimizer这几个软件。
安装时间可能会几十分钟到几个小时不等，主要是机器的配置网速等原因会造成影响。
2.2.3 安装完成
如果显示Nginx: OK，MySQL: OK，PHP: OK
![1456998386104931.png](http://upload-images.jianshu.io/upload_images/977602-bbf2c977dd76f6e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
并且Nginx、MySQL、PHP都是running，80和3306端口都存在，并Install lnmp V1.2 completed! enjoy it.的话，说明已经安装成功。
安装时间比较长，我花了刚好30分钟。最后的界面可能和上面教程不同，只要出现enjoy it就行。下面是我安装完成的截图：
![1456998468383746.png](http://upload-images.jianshu.io/upload_images/977602-37030a6e4b85a8b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3 添加虚拟主机
经过上面的操作，已经安装完成网站的运行环境LNMP，接下来需要创建虚拟主机添加网站。（下面的操作说明搬自[banwagong.com](http://banwagong.com)）

◆通过SSH连接到VPS，连接后输入命令 lnmp vhost add 。创建虚拟主机的过程是一个交互式的页面，集中截图到了一起，这里就细说一下。
◆首先会要求你输入域名，输入好域名回车，会显示是否添加其他域名。我在这里一般是选n，不添加其他域名，然后在通过301重定向不带www的域名到带www的域名。
◆然后就是网站的路径，默认的是/home/wwwroot/yourdomain 。如果不打算更改的话，直接回车就好，想自定义路径的话直接输入自己想要的路径就好了。
◆然后就是是否允许Rewrite。这里建议选择y。lnmp自带了几种常用网站的伪静态规则，因为我们要安装的是wordpress，直接输入wordpress就可以了。
◆再下面一部是是否开启访问日志。搬瓦工小硬盘的套餐的话，不建议开启，毕竟硬盘资源有限。
◆再接下来就是创建数据库，这里如果要创建的话，会创建成一个用户名和数据库名相同的。
◆如果选择y的话，会要先验证MySQL的root密码。验证后会让你输入数据库名，回车后会提示你，已经创建了一个和数据库名相同的用户名。然后就是输入数据库的密码。
◆再回车以后就跳到最后一步，按任意键创建虚拟主机。
![1456999197849301.png](http://upload-images.jianshu.io/upload_images/977602-d27d88a3965a5880.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当出现上图最后的画面时，你的虚拟主机已经创建成功了。
当然，这里的各项配置是可以通过修改配置文件进行更正的。所以没必要太纠结。通过vi修改或者下载到本地修改都可以。虚拟主机配置文件在：
/usr/local/nginx/conf/vhost/域名.conf

4 安装WordPress
通过上面的步骤已经安装好了VPS搭建网站所需的环境并创建好了主机，接下来就是上传网站文件完成网站的安装。
4.1 上传WordPress网站文件
我们需要一款ftp软件，这里使用的是[Filezilla](https://filezilla-project.org/)。之前的环境搭建中，并没有安装ftp服务，所以使用sftp上传网站文件。
在Filezilla主页中点击文件→站点管理器，具体设置如下图，注意端口要填正确，协议选择sftp。登录类型选择正常就好，用户名密码填好点击连接即可。
![1456999357829592.png](http://upload-images.jianshu.io/upload_images/977602-ab728992df1bb1a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
连接之后建议先进入/home/wwwroot/default 删除其中的如下图所示选中的文件，同时修改phpmyadmin的目录名，改为不容易猜到的。
很多人问这一步的原因，根据评论中[SEOIMO](http://www.seoimo.com/)所说：
default这个文件夹是系统默认的，里面是一些安装的信息，比如数据库和探针地址，为了安全起见，应该将里面文件改名或重建。而域名是建立在/home/wwwroot/文件夹下的，和default同层的。 除了数据库外，不用太在意default里面的内容，因为建站并不在此文件夹内。

![1456999382935587.png](http://upload-images.jianshu.io/upload_images/977602-58a17fb6d4630819.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后进入到网站的安装目录，即上面的www.jwcyber.com文件夹，把网站的源文件上传到根目录里就可以了。当然，先得先去[中文官网](https://cn.wordpress.org/)把wordpress的安装文件下载下来。

如果用Filezilla直接上传WP的网站文件，由于全是小文件，这将是一个非常漫长的过程。所以先将网站文件压缩成zip压缩包，上传到VPS后再解压。打包的时候直接多选文件打包成1.zip，方便解压。

压缩包上传完成后，通过SSH连接到VPS，进入网站的安装目录，命令为：
cd /home/wwwroot/www.jwcyber.com (换成你自己的安装目录即可，注意cd后面有空格）

进入后执行命令 unzip 1.zip 回车即可。
![1456999429630405.png](http://upload-images.jianshu.io/upload_images/977602-9656384a9c45c3c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
解压后要使**WordPress的****网站文件直接位于www.jwcyber.com文件夹下**，如下图，否则使用Filezilla移动一下文件。
![1460913548109177.jpg](http://upload-images.jianshu.io/upload_images/977602-f0fb66f5789719ff.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后等待域名的解析生效以后，就可以安装网站了。
4.2 安装网站
输入网址www.jwcyber.com，如果出现的是LNMP界面，则在VPS的管理界面里面重启一下VPS；如果是下面的WordPress的界面，证明前面的操作都没有问题，可以进行WordPress的配置了。按照下面的截图进行配置就行了。
![1456999470114025.png](http://upload-images.jianshu.io/upload_images/977602-0498d2a9e31da793.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![1456999487346443.png](http://upload-images.jianshu.io/upload_images/977602-0c559d2a27d1587d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![1456999503100994.png](http://upload-images.jianshu.io/upload_images/977602-9a807a922c7abad6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![1456999532700394.png](http://upload-images.jianshu.io/upload_images/977602-5bf4a3d562aa91ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**欢迎使用WordPress！**
到这里，用VPS+LNMP+WordPress搭建个人网站就基本完成了。
![1456999552136385.png](http://upload-images.jianshu.io/upload_images/977602-a3cf24dfe5ce93d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
写在后面
根据上面的教程，可以零基础利用VPS+LNMP+WordPress搭建出一个网站来，不过最终的到的只是一个可以访问的界面，离真正的个人网站还差得很远。
还需要下功夫的地方主要有两个：界面和内容。WordPress很强大，众多的主题和插件，需要慢慢去研究。而内容，则是核心所在，没有内容，网站做得再漂亮也是白搭。我其实对于我是否能够把这个博客长久运行下去也是没有十足信心的，我基本没怎么发过原创内容，加上自己写东西确实不行，想要多产出内容是很难的。前面说过我想用这个网站干什么，那么今后就朝着那些方向前进吧。
不过首先，得把网站的内容完善了，现在只是随便装了一套主题。下一篇文章应该就是[WordPress使用中遇到的一些问题](http://www.jwcyber.com/tips-for-wp/)，包括301重定向，编辑器说明等。
加油！
WordPress使用常见问题
[1 提示需要输入FTP信息](http://www.jwcyber.com/tips-for-wp/#1_FTP)
[2 301重定向jwcyber.com到www.jwcyber.com](http://www.jwcyber.com/tips-for-wp/#2_301jwcybercomwwwjwcybercom)
[3 WordPress只显示一个主题](http://www.jwcyber.com/tips-for-wp/#3_WordPress)
[4 自带编辑器不够用](http://www.jwcyber.com/tips-for-wp/#4)
[5 为文章添加目录](http://www.jwcyber.com/tips-for-wp/#5)
[6 网站导航菜单](http://www.jwcyber.com/tips-for-wp/#6)
[7 Gravatar头像不能加载或者加载缓慢](http://www.jwcyber.com/tips-for-wp/#7nbspGravatar)
[8 使用百度统计分析网站](http://www.jwcyber.com/tips-for-wp/#8)
[9 添加站点地图](http://www.jwcyber.com/tips-for-wp/#9)
[10 文章内容分页](http://www.jwcyber.com/tips-for-wp/#10)

转载请注明：[jwcyber](http://www.jwcyber.com) » [VPS+LNMP+WordPress搭建个人网站/博客](http://www.jwcyber.com/build-site/)

