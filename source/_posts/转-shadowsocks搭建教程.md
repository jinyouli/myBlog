---
title: (转)shadowsocks搭建教程
date: 2017-02-24 16:23:50
categories:
tags: 搬瓦工
---
##前言
在G+混了一段时间后，发现部分小伙伴的翻墙事业还没有踏上康庄大道。在这儿的应该不少都是G粉，在Google服务全线被墙的严峻形势下翻墙不利索肿么能行。于是特地花了几天时间，写了篇比较详尽的Shadowsocks翻墙教程分享给大家，涵盖了从零开始的方方面面。
<!-- more -->
原文：http://shadowsocks.blogspot.jp/

##前言
 在G+混了一段时间后，发现部分小伙伴的翻墙事业还没有踏上康庄大道。在这儿的应该不少都是G粉，在Google服务全线被墙的严峻形势下翻墙不利索肿么能行。于是特地花了几天时间，写了篇比较详尽的Shadowsocks翻墙教程分享给大家，涵盖了从零开始的方方面面。

友情提醒：别说你没基础、看不懂。所谓的“不会”都是懒的借口，而且教程不是看会的，是动手跟着教程一步一步做会的。如果你懒得动脑又懒得动手，那你现在已经可以点击右上方的X了。如果你已经准备好了，那么follow me，走你！特别提醒：以下的每一个部分都非常重要，你少看一句话都可能影响后续步骤的进行和最终的使用效果。

###Shadowsocks特点

1.省电，在电量查看里几乎看不到它的身影；
2.支持开机自启动，且断网无影响，无需手动重连，方便网络不稳定或者3G&Wi-Fi频繁切换的小伙伴；
3.可使用自己的服务器，安全和速度的保证；
4.支持区分国内外流量，传统VPN在翻出墙外后访问国内站点会变慢；
5.可对应用设置单独代理，5.0之后的系统无需root。

我自己的感受：随机启动24小时后台运行，占内存10MB以内，基本不怎么耗电，跟人直接置身墙外使用手机的感受差不多。

###VPS推荐与支付
 Shadowsocks的正常使用需要服务端，其实所有的翻墙方式都需要服务端，搭建服务端需要你拥有一个属于自己的VPS。下面是我自己精挑细选出来的三家VPS供应商，如果你坚持认为我是在给这三家VPS打广告，你就不用往下看了。这三家我都在用，感觉不错，当然你也可以选择其他家的VPS产品。

DigitalOcean：

注：DigitalOcean部分域名被墙，如遇显示不正常或无法访问，全站翻墙访问即可，在上面购买的VPS不受影响。

KVM架构    512MB内存  20GB硬盘   1000GB流量/月 5美元/月（折合人民币30元/月）
（楼主自己在用）
https://www.digitalocean.com/?refcode=03e3e84b8f22
（使用本链接注册账户立即到账10美元）

 搬瓦工（BandwagonHOST）：

注：搬瓦工域名在部分地区被墙，可能需要翻墙访问，但在上面购买的VPS不受影响。

OpenVZ架构 512MB内存  10GB硬盘 1000GB流量/月 19.99美元/年（折合人民币11元/月）
（强力推荐，官方最新上架的中国直连特惠主机，只有Los Angeles节点）
https://bandwagonhost.com/aff.php?aff=1285&pid=34
 （温馨小提示：注意在“Billing Cycle”的下拉菜单里选择“$19.99 USD Annually”比月付省66%）

特惠主机数量一般有限，可能随时断货。

OpenVZ架构 1024MB内存  20GB硬盘 2000GB流量/月 39.99美元/年（折合人民币20元/月）
 （官方最新上架的中国直连特惠主机，只有Los Angeles节点）
https://bandwagonhost.com/aff.php?aff=1285&pid=35
 OpenVZ架构 512MB内存  10GB硬盘 1000GB流量/月 19.99美元/年（折合人民币11元/月）
（官方最新上架的特惠主机，只有Fremont节点）
https://bandwagonhost.com/aff.php?aff=1285&pid=36

OpenVZ架构 256MB内存  10GB硬盘 500GB流量/月 19.99美元/年（折合人民币11元/月）
（楼主自己在用，多节点机房中最便宜的）
https://bandwagonhost.com/aff.php?aff=1285&pid=12

支付宝购买搬瓦工VPS详细教程：点击上面的优惠链接后，在“Billing Cycle”的下拉菜单里选择“$19.99 USD Annually”或者“$49.99 USD Annually”，“Location”保持默认即可，然后点击“Add to Cart”按钮，网页跳转后，点击“Checkout”，在新页面的“Your Details”部分输入你的个人资料以及注册信息，除了“Address 2”和“Company Name”之外，其余均为必填项目，注意，尽量填写真实信息，如果你在中国大陆，“Country”请选择“China”，以免被系统判定为欺诈而注册失败。“Payment Method”里选择“Credit Card and AliPay (Stripe)”，勾选“I have read and agree to the Terms of Service”，最后点击“Complete Order”，页面跳转后，点击绿色的大按钮“Pay now”，然后会弹出一个小窗口，先在“电子邮箱”一栏里输入你的支付宝账号（此处必须为邮箱账号），然后点击下方的“支付宝”按钮，此时系统会自动给你支付宝账号绑定的手机号发送一条带验证码的短信，在“输入发送至xxxx的校验码”下方输入6位数的短信校验码，然后在最下面的“身份证号码后5位”框里输入你的身份证号后5位，最后点击蓝色的“Pay now $xx”按钮，完成支付。

 Linode：

注：Linode域名在部分地区被墙，可能需要翻墙访问，但在上面购买的VPS不受影响。

Xen架构     1024MB内存  24GB硬盘   2000GB流量/月 10美元/月（折合人民币60元/月）
（只推荐给对连接速度和网络延迟有极致追求的用户，楼主自己也在用）
https://www.linode.com/?r=69edd5eafe47ed8a7e128c057f3367a90ce51135
（注册时Referral Code处输入69edd5eafe47ed8a7e128c057f3367a90ce51135）

Linode只能使用信用卡支付，官方会随机手工抽查，被抽查到的话需要上传信用卡正反面照片以及可能还需要身份证正反面照片，只要材料真实齐全，审核速度很快，一般一个小时之内就可以全部搞定。账户成功激活以后，就可以安心使用了。

DigitalOcean和搬瓦工两家的VPS都支持PayPal付款，DigitalOcean也可以选择在账单里绑定信用卡进行支付。

值得说明的是无论是注册这三家VPS还是注册PayPal，尽量填写真实信息，这样一旦遇到审核会更容易通过，注册的时候遇到国家地区一定要如实选择你所在的真实地区如“China”，以防被系统判定为欺诈。

2015年6月26日更新：搬瓦工目前已经支持使用支付宝付款，支付时请注意选择带AliPay字样的支付方式。

关于支付的重要补充说明(必看！)：有小伙伴反映PayPal绑定银联借记卡之后无法支付搬瓦工的VPS，DigitalOcean没问题，经过我调查之后，发现有人很顺利的就用银联卡支付成功，有人则死活不行，所以问题的具体原因不好说。但这里给出有效的解决方案：绑定信用卡。没有信用卡的学生党可以点击这里http://dwz.cn/wbZHy(此处为官方提供的短链接，网址安全，可放心使用，是一家在国内非常知名的虚拟信用卡服务商)申请一张虚拟信用卡，注册快速，可用普通网银充值，经楼主实测，可顺利支付搬瓦工。而且该卡还可以绑定Google Wallet，Google Play上面的软件和硬件可随便买（提醒：Google Wallet有一套很复杂的安全检测机制，无论你购买什么东西，如果不小心遇到订单被取消的情况，那都很正常，这是题外话了）。此外， DigitalOcean审核较为严格，尤其是对于选择了信用卡作为支付方式的用户，可能会要求你上传身份证明以及信用卡照片什么的，而且审核过程也需要等待，如果你怕麻烦，我建议你直接使用PayPal支付，方便快捷。通过审核开始正式使用后，一般就没什么问题了，多点耐心。

###VPS深入说明与选择
 我简单解释一下三家差价比较大的原因和技术特点：

OpenVZ为不完全虚拟化技术，每个VPS账户共享母机内核，易受同一母机下其他VPS的影响，几乎不能单独修改内核。Xen和KVM为完全虚拟化技术，各VPS之间互相独立，基本互不影响，而且可以任意修改内核。

这三种架构对我们搭建shadowsocks服务器来讲最直观的区别就是，Xen和KVM可通过系统内核修改来优化服务器，大幅度提升shadowsocks的连接速度，尤其体现在晚高峰的时候。

我在同一时间段用100MB的文件简单的在自己的三台VPS上面测试了一下shadowsocks的连接速度：

搬瓦工（19.99美元/年）的平均下载速度在1.36-3.43Mbps之间（174-439KB/S），也就是说速度表现不是很稳定，速度快的时候也可以看下YouTube 720p，速度慢的时候YouTube 480p还是没有问题的。

DigitalOcean（5美元/月）的平均下载速度稳定维持在3.70Mbps以上（474KB/S），这个速度已经是我本地物理带宽的上限，所以VPS的速度上限未知，基本在大部分时候YouTube 1080p都可以流畅播放，任意时刻YouTube 720p都没问题。

Linode（10美元/月）的上传下载速度均达到带宽满载，官方给出的数据是“40 Gbit Network In / 125 Mbit Network Out”，由于楼主本地带宽有限，有热心小伙伴分享了他在联通LTE网络环境下的测试结果，数据显示速度可达60M以上(7.83MB/S)，略恐怖，意味着任意时刻YouTube 1080p秒开，只要你的带宽够，一般来说看4K也是没有问题的。Linode除了速度快之外，还有一个杀手锏就是提供日本节点，ping值70ms以内，有超低网络延迟需求的小伙伴可以重点考虑下。

个人建议，对连接速度和稳定性尤其是网络延迟有极高要求的首选Linode（只有最快，没有更快），有较高要求的推荐DigitalOcean（一分价钱一分货），对于普通用户来讲，搬瓦工就可以（性价比高）。

###VPS节点的选择
 搬瓦工各节点测试IP：
Los Angeles：   104.194.78.3
Florida：       74.121.150.3
Phoenix：       198.35.46.2（可在控制面板里切换到这个机房）

DigitalOcean各节点测试域名：
San Francisco： speedtest-sfo1.digitalocean.com
新加坡：        speedtest-sgp1.digitalocean.com
New York：      speedtest-ny1.digitalocean.com
Amsterdam：     speedtest-ams1.digitalocean.com
英国伦敦：      speedtest-lon1.digitalocean.com

Linode各节点测试域名：
Tokyo,JP：      speedtest.tokyo.linode.com
Fremont,CA：    speedtest.fremont.linode.com
Newark,NJ：     speedtest.newark.linode.com
Atlanta,GA：    speedtest.atlanta.linode.com
Dallas,TX：     speedtest.dallas.linode.com
London,UK：     speedtest.london.linode.com

请在CMD下自行使用“ping IP -t”或“ping 域名 -t”命令来测试不同位置的机房与你的电脑之间的ping值以及丢包率（Ctrl+C退出测试）。

如果还是不知道该选择哪个节点的小伙伴，搬瓦工一般选用Los Angeles节点居多，DigitalOcean一般选用San Francisco节点居多(都在美国西海岸)，而Linode一般选择“Tokyo,JP”(日本节点)或者“Fremont,CA”(美国西海岸)，由于Linode日本节点ping值很低(70ms左右)、销售火爆，可能会一时无货，如果遇到无货，等一会再试试(我也是刷新了一会就有了)。一开始节点选择的不理想也不要紧，以后还可以方便的切换机房。

特别对比：Linode的Fremont,CA节点与DigitalOcean的San Francisco节点相比，同在美国西海岸，ping值和丢包率基本差不多，但Linode的网速明显更快。

###VPS的创建与使用
 搬瓦工默认系统为Centos 6 x86，保持默认即可；DigitalOcean创建VPS的时候(Create Droplet)选择CentOS 6.5 x64（自作聪明选择CentOS 7的人是没救的）；Linode的详细操作说明见下方。

针对搬瓦工的专属补充教程：可能由于搬瓦工官方发现很多人都在他家的VPS上面搭建了Shadowsocks，于是机灵的官方在自家的控制面板里集成了一键搭建服务，大大降低了新手搭建难度，实现了傻瓜化的操作。我简述下流程：点击“Services”里的“My Services”，点击“KiwiVM Control Panel”，这时会跳转到一个新页面。将新页面左侧的滚动条拉到底，找到“Shadowsocks Server”字样并点击进入，然后点击“Install Shadowsocks Server”，几秒钟之后显示Completed的字样就代表完成了。这时候点击“Go back”或者直接点击左侧的“Shadowsocks Server”，你会看到出现了一个叫做“Shadowsocks server controls”的东西，上面有默认的加密方式(encryption)、服务器端口(port)以及密码(password)，你可以直接使用默认的，也可以点击旁边的“Change xx”按钮进行修改，最后检查一下Status是不是Running，不是的话就点一下旁边的“Start”。至此，你的Shadowsocks服务端就搭建完成了，你可以直接跳转到本教程后面的客户端配置部分了(搬瓦工也自带了简易的客户端配置说明“What's next?”)。注意，此方法为搬瓦工专属，仅供懒人和纯新手，你如果想在更好更快的其他家的VPS上面搭建Shadowsocks服务，你还是需要老老实实的学习下面的手工搭建方法，而且我强烈建议你如果有能力也有精力，最好掌握搭建的原理，对于你以后维护自己的VPS或者再学习搭建其他的翻墙服务都大有脾益。

DigitalOcean创建VPS过程补充说明：点击网页左上角绿色的“Create Droplet”按钮，输入你的“Droplet Hostname”(英文)，然后“Select Size”选择你的付费套餐，“Select Region”选择按照上面的教程测出来的对你最快的节点，“Select Image”选择CentOS 6.5 x64，没提到的选项保持默认即可，最后点击最下方的“Create Droplet”。

注册完毕后，你已经获得了你VPS的IP，SSH端口，root密码。去下面的地址下载putty，用于在你的windows系统上远程登陆你的VPS。注意：用putty登录VPS时输入的密码是不可见的，正常输入完毕后回车即可：
http://www.putty.org/


![图片.png](http://upload-images.jianshu.io/upload_images/977602-992135e54103bbe0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 搬瓦工需要在“My Services”里进入“KiwiVM Control Panel”点击“Root password modification”来获得root密码，SSH端口在邮件或者控制面板里可看到，用户名是root；

特别警告：搬瓦工的控制面板里有个“OpenVPN Server”选项，可以一键搭建OpenVPN服务端，千万别去使用。否则，到时候你的VPS彻底挂了可别怪我没有提前提醒你(不作死就不会死)，详细原因请自行查看教程后面的进阶答疑部分。

DigitalOcean则是把密码发到了你邮箱里，而且DigitalOcean在首次登陆VPS的时候系统会提示你修改，你再输入一次原密码后连续输入两次新密码就OK了，DigitalOcean默认SSH端口为22，用户名是root。

Linode的详细操作说明：Linode账户在绑定信用卡激活后，就可以创建VPS了。值得说明的是，Linode的控制面板较为复杂，当然伴随而来的是功能也更强大。在“Linodes”里选择套餐，左下角选择机房位置，完成后可看到给你分配的IP以及主机名称，点击主机名称比如“linode654321”，然后点击“Deploy an Image”，在“Image”里面选择“CentOS 6.5”，在“Root Password”的方框里填入你的root密码，然后点击下方的“Deploy”按钮，大概1分钟左右就会创建完毕。点击“Dashboard”下的“Boot”按钮，你的VPS就开始启动了，启动完成后，就可以使用putty来操作你的VPS了，默认SSH端口为22。


![图片.png](http://upload-images.jianshu.io/upload_images/977602-0e4fbb172e4422d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 下面的内容需要你有一点点linux的基本知识，用过windows下CMD的小伙伴应该能很快上手，全部命令和内容都可以复制下来通过右键直接粘贴到putty里执行。

vi编辑器基本用法扫盲（新手必读）
http://linux.chinaunix.net/doc/office/2005-01-24/898.shtml
（基本用法）
http://linux.vbird.org/linux_basic/0310vi.php
（图文详解）

###Shadowsocks服务端搭建
下面的命令，需要一行一行的执行，每输入一行命令，回车执行，如果没有报错，即为执行成功，出现确认提示的时候，输入 y 后，回车即可。每行命令可以复制后在putty里右键粘贴，回车执行。

yum install epel-release
yum update
yum install python-setuptools m2crypto supervisor
easy_install pip
pip install shadowsocks


![图片.png](http://upload-images.jianshu.io/upload_images/977602-91dc4f8fbe987aa2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![图片.png](http://upload-images.jianshu.io/upload_images/977602-ef87040dfc10b524.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

继续执行命令

vi /etc/shadowsocks.json

此时按 i 键进入编辑模式，putty黑框的左下角会出现 -- INSERT -- 字样，然后一次性复制下面的内容（复制之前记得修改8388和yourpassword为你自己的端口号和密码，此端口号不是你的SSH端口号，而是你在手机或电脑上的shadowsocks客户端连接VPS上搭建的服务端的端口号，范围 1 - 65535 ，只要不和现有的端口号如SSH端口冲突都可以，记下你修改的端口号和密码，待会儿在配置手机和电脑的客户端时还要用到），在putty里右键，此时复制的内容应该已经粘贴到了putty里

----------以下内容为复制内容----------
{
    "server":"0.0.0.0",
    "server_port":8388,
    "local_port":1080,
    "password":"yourpassword",
    "timeout":600,
    "method":"aes-256-cfb"
}
----------以上内容为复制内容----------


![图片.png](http://upload-images.jianshu.io/upload_images/977602-bce0f9878ea2d151.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 复制完成后，按 Esc 键退出编辑模式，此时putty黑框左下角的 -- INSERT -- 字样消失，按下 : 键，输入 wq 后回车，此时文件保存完毕并退出了vi编辑器。（“ : ”的输入方法为“Shift+字母L右侧的分号键”）

继续执行命令

vi /etc/supervisord.conf

此时你应该能看到很多英文内容，按 i 键再次进入编辑模式，putty黑框的左下角会出现 -- INSERT -- 字样，用方向键将光标调整至文件尾部的空行处，然后一次性复制下面的内容，在putty里右键，此时复制的内容应该已经粘贴到了putty里

----------以下内容为复制内容----------
[program:shadowsocks]
command=ssserver -c /etc/shadowsocks.json
autostart=true
autorestart=true
user=root
log_stderr=true
logfile=/var/log/shadowsocks.log
----------以上内容为复制内容----------


![图片.png](http://upload-images.jianshu.io/upload_images/977602-6e5fefb8cfe01f0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 复制完成后，按下回车键给文件尾部留出空行，然后按 Esc 键退出编辑模式，此时putty黑框左下角的 -- INSERT -- 字样消失，按下 : 键，输入 wq 后回车，此时文件保存完毕并退出了vi编辑器。

继续执行命令

vi /etc/rc.local

此时你应该能看到几行英文内容，按 i 键再次进入编辑模式，putty黑框的左下角会出现 -- INSERT -- 字样，用方向键将光标调整至文件中部的空行处，然后一次性复制下面的内容，在putty里右键，此时复制的内容应该已经粘贴到了putty里

----------以下内容为复制内容----------
service supervisord start
----------以上内容为复制内容----------

![图片.png](http://upload-images.jianshu.io/upload_images/977602-3a71f048ffe0c068.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 复制完成后，按 Esc 键退出编辑模式，此时putty黑框左下角的 -- INSERT -- 字样消失，按下 : 键，输入 wq 后回车，此时文件保存完毕并退出了vi编辑器。

最后执行命令

reboot

此时，你的VPS重新启动，服务端已经完全配置完毕，putty会弹出一个连接已断开的提示框，关闭即可(不是报错)。

![图片.png](http://upload-images.jianshu.io/upload_images/977602-c0a5a6fef7c7c0cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

小提醒：搬瓦工的VPS在执行完reboot命令后有时会遇到重启失败的情况，这时候进入控制面板，看一下“Status”是不是“Running”，如果不是的话，点一下“Actions”里的“start”按钮即可。

###Shadowsocks客户端配置
 至此，shadowsocks的服务端已经部署完成。剩下的就是下载客户端安装到你的手机和电脑上，记得修改客户端的相关设置保持和你的服务端参数一致哦。

Android客户端下载链接
https://play.google.com/store/apps/details?id=com.github.shadowsocks
推荐在Google Play下载，自动适配你的系统版本，以免出现问题。
https://github.com/shadowsocks/shadowsocks-android/releases

电脑客户端端下载链接（Windows、Mac OS X）
http://sourceforge.net/projects/shadowsocksgui/files/dist/
小提醒：Windows 7用户下载Shadowsocks-win-x.x.x.zip，Windows 8用户下载Shadowsocks-win-dotnet4.0-x.x.x.zip。

iOS客户端端下载链接
https://itunes.apple.com/cn/app/shadowsocks/id665729974?mt=8

Android手机客户端配置示例（以上述服务端配置为例）：
注意：已经root手机的小伙伴请勿授予root权限，以免发生未知问题。

服务器：你的VPS IP地址（非0.0.0.0）
远程端口：8388
本地端口：1080
密码：yourpassword
加密方法：AES-256-CFB
路由：绕过局域网及中国大陆地址
全局代理：勾选
UDP转发：建议勾选，如有问题则取消勾选
自动连接：勾选

电脑客户端配置示例（以上述服务端配置为例）：
（示例客户端版本：Shadowsocks-win-2.1.6.zip (144.9 kB)，系统Windows 7，如遇无法启动的情况，请右键以管理员身份运行）

服务器 IP：你的VPS IP地址（非0.0.0.0）
服务器端口：8388
密码：yourpassword
加密：aes-256-cfb
代理端口：1080
备注：随便写

右键任务栏飞机小图标，勾选“启用代理”、“开机启动”。

注：新版的shadowsocks电脑客户端已经支持一键切换系统代理，无需浏览器插件，内置可编辑的PAC服务，并提供HTTP代理，兼容IE。当然，你也可以继续使用Chrome浏览器配合SwitchySharp代理插件使用，SwitchySharp的具体配置如下图所示：


![图片.png](http://upload-images.jianshu.io/upload_images/977602-cfc9e2bc4270ade9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![图片.png](http://upload-images.jianshu.io/upload_images/977602-93b953d4b6e71ba5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![图片.png](http://upload-images.jianshu.io/upload_images/977602-750a8d17008001ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 以上内容在搬瓦工和DigitalOcean以及Linode三家的VPS上已经全部测试通过，无误。

对于能够成功连接但觉得上网速度慢的小伙伴：

1.请先确认你自己有没有严格按照教程选择对你最快的节点；
2.请将server_port由默认的8388改为其他端口；
3.如果速度还是不满意，那么请将你的VPS更换为DigitalOcean或者Linode（你要始终相信一分价钱一分货）。
4.南方部分地区电信用户的国际出口遭到电信人为限制，如果当地有“国际精品网”业务，开通后可立即完美解决这个问题。如果不愿意给电信交保护费，建议尝试使用DigitalOcean的新加坡节点或者Linode的日本节点，如果速度还是不满意，换移动和联通吧。

###进阶答疑
 本答疑会根据大家的反馈以及shadowsocks的不断更新而不定期更新，进阶部分针对已经具备一定基础的非新手，这部分内容遇到任何问题请自行Google。

1.Android 5.0的Shadowsocks为什么耗电量非常高？

Android 5.0的电量统计模块把所有经过shadowsocks代理的流量所产生的耗电量都算在了shadowsocks上，因此看起来会很耗电，比如你Chrome浏览器的电量都被算到了shadowsocks头上，实际上还是很省电的。

2.如何查看当前VPS上的Shadowsocks服务端版本号？

pip show shadowsocks

3.以后如何升级VPS上的Shadowsocks服务端？

pip install --upgrade shadowsocks
reboot

4.我搭建过程中不小心出错，想重新来过，如何重装VPS的系统？

搬瓦工：VPS控制面板里，Install new OS
DigitalOcean：VPS控制面板里，Destroy，Rebuild
Linode：VPS控制面板里，Rebuild

5.我开始选择的节点线路不理想，如何切换机房？

搬瓦工：VPS控制面板里，Migrate to another DC，无需重新搭建Shadowsocks服务端。
DigitalOcean：新建一台你想要机房的VPS，删除原来的，需要重新搭建Shadowsocks服务端。
Linode：新建一台你想要机房的VPS，删除原来的，需要重新搭建Shadowsocks服务端。

6.如何配置多账户？

小提示：Shadowsocks支持一个账户在多个终端同时使用，一般人没有配置多账户的必要。所以如果你看不懂，那你还是别折腾了。

{
    "server":"0.0.0.0",
    "port_password":{
        "8388":"password1",
        "8389":"password2",
        "8390":"password3",
        "8391":"password4"
    },
    "timeout":300,
    "method":"aes-256-cfb"
}

友情提醒：GFW目前是根据流量检测分析匹配统计学模型的方式来判断你是否在翻墙，换言之，你用什么方式翻墙并不重要，重要的是你和服务器之间的流量特征是否像是在翻墙。一旦匹配，既对你进行有罪推论，轻则限速，重则彻底封锁IP。在IPv4地址已经枯竭的今天，可用的美国IP地址会越来越少，所以不建议将自己的账号分享多人使用，以防被封。

7.为什么我的shadowsocks在刚搭建好的时候速度很快用了几天后速度就变慢了甚至网页都很难刷出来？

出现这种情况有多种可能性：

①shadowsocks长时间保持不间断连接会被GFW根据流量模型分析判断出你可能在翻墙(原理见上面的友情提醒)，于是进行主动干扰，轻则限速，重则切断你和服务端的连接。解决方法：切换一下你的网络，比如从Wi-Fi切换到3G或者从3G切换到Wi-Fi或者直接断开网络连接，等待10分钟以后，一般即可恢复正常。PS：每天晚上睡觉前关闭手机的网络连接会大大减小此种情况发生的概率；而将自己的VPS分享给多人使用则可能大大增加此种情况发生的概率，请自行斟酌。值得说明的是，VPN最容易受到此类干扰，而shadowsocks作为可自定义端口的私有协议代理已经是最不容易被干扰的翻墙方式之一了。

②机房的QoS策略。解决方法：将shadowsocks服务端的server_port改为常见端口。

③本地线路抽风，你所使用宽带运营商的国际出口出现问题。比如最近南方电信部分地区国际出口严重不稳定(电信人为限制)。解决方法：<1>先尝试一下DigitalOcean的新加坡节点或者Linode的日本节点；<2>如果当地有“国际精品网”业务，开通后可立即完美解决这个问题；<3>如果不愿意给电信交保护费，那么就换家运营商吧，移动和联通都没问题。

④VPS间歇性抽风。无论你选择哪家供应商的VPS，都可能遇到有时候线路抽风、VPS速度慢或者不正常。不同的是，越是价位高的VPS出现抽风情况的可能性越低，越是价位高的VPS出现抽风情况时能保证的最低连接速度越高。出现这种情况的可能性比较低，我手头的无论是搬瓦工还是DigitalOcean以及Linode暂时未遇到线路抽风情况。

⑤搬瓦工的年付VPS为OpenVZ架构，同一母机下的VPS越多，同一时间段使用的人越多，速度就越慢。解决方法：一分价钱一分货，将VPS更换为DigitalOcean或者Linode。

⑥中国的国际出口带宽有限，晚高峰时段可能出现网络拥堵，速度多少会受影响，但这种情况起码白天的速度应该是没问题的。

⑦如果你在VPS上搭建了VPN并且经常使用，尤其是OpenVPN，请立即停止使用。VPN协议特征明显，GFW可以非常容易的检测到，从而盯上你的IP，轻则限速，重则彻底屏蔽。常见VPN协议根据易受干扰的程度从大到小依次为：OpenVPN > PPTP > L2TP > IPSec，尤其是OpenVPN，GFW已经可以实现对其定点清除(同样遭此待遇的还有SSH翻墙)。如果你想让自己VPS的IP快速报废，那么就请尽情的使用搬瓦工的控制面板搭建OpenVPN吧。重要提醒：在不明所以的情况下尽量不要在自己的VPS上搭建其他杂七乱八的翻墙服务尤其是一些早已过时和落后的翻墙方式，翻墙手段宜新不宜旧，只搭一个Shadowsocks是最能保证你翻墙效果和服务器稳定的好策略。

⑧其他：偶尔的速度慢或者连不上都是正常的，但如果经常性的速度奇慢或者连不上那就不正常了。

8.为什么是CentOS？

作为服务器而言，永远都是稳定性压倒一切。而CentOS简单易用，上手快速，业界公认的稳定，且易于维护，是服务器操作系统首选。

9.为什么是Python版？

Python版的Shadowsocks易部署，后期升级维护都非常方便，相当适合新手，支持的特性也最多，稳定性好，运行效率高。

10.为什么使用supervisord？

与繁琐的带参执行方式相比，service命令在CentOS系统里使用起来更加灵活方便，比如：

①启动Shadowsocks服务端：service supervisord start
②关闭Shadowsocks服务端：service supervisord stop
③重启shadowsocks服务端：service supervisord restart

###错误排查
 已经成功的小伙伴可以直接略过这部分了。

温馨提醒：在怀疑教程的任何一个地方之前，请先怀疑你自己。

服务端搭建成功的唯一衡量标准是在手机或者电脑客户端正确配置后能否顺利的访问被屏蔽的网站，无论是电脑还是手机，只要有一个终端能够成功翻墙即视为服务端搭建成功，出现所谓的可以连接但无法上网其实还是服务端或者客户端的配置有问题，认真按照下面的步骤一步步排错吧。

遇到问题的小伙伴请先认真仔细阅读这两篇文章
http://linux.chinaunix.net/doc/office/2005-01-24/898.shtml
http://linux.vbird.org/linux_basic/0310vi.php
上面的链接为vi编辑器基本用法扫盲（新手必读）

最容易出现问题的地方，就是vi编辑器的使用，如果你在执行vi命令后没有按 i 键进入编辑模式就直接复制粘贴，会造成粘贴内容的首行被覆盖，从而导致错误；或者你在粘贴内容后，没有以正确的方式保存退出，同样会出现问题。

如果你在执行完reboot命令后，手机端无法连接，请先确保你的手机端配置正确，并且网络环境良好(参照上面的Android手机客户端配置示例)。然后用putty登入你的VPS后通过以下方式逐步排查：

1.执行命令service supervisord start，执行完毕后如果没有报错，手机端也可以正常连接，那么问题出在vi /etc/rc.local这个环节，请重新检查该文件配置；如果手机端依然无法连接，请继续往下看。

2.执行命令ssserver -c /etc/shadowsocks.json，执行完毕后如果没有报错，手机端也可以正常连接，那么问题出在vi /etc/supervisord.conf这个环节，请重新检查该文件配置；如果手机端依然无法连接，请继续往下看。

3.如果依次进行完以上两步后，手机端依然无法连接，那么问题出在vi /etc/shadowsocks.json环节，请重新检查该文件配置。

4.如果以上三个文件的配置问题都解决后，执行reboot命令后，手机端依然无法连接，那么说明你在教程最开始的5行命令没有正确执行，在搬瓦工和DigitalOcean以及Linode官网的控制面板里重装VPS系统后，按照教程认认真真仔仔细细的重新来过吧。

###尾巴
 整个教程到这里就结束了，我按照自己写的内容重新搭建了一遍没有遇到问题，大家有什么问题可以在帖子下面提问，在提问之前请先确保你是严格按照教程一步步认真往下执行了的。关于远程登陆VPS后linux的使用问题最好先Google一下。另外，本教程可能根据大家反馈的情况随时更新，遇到问题请点击下面的链接查看原帖：
http://shadowsocks.blogspot.com/2015/01/shadowsocks.html

https://plus.google.com/103234343779069345365/posts/Xce4EJpLGhX

点击如下链接地址可查看更多教程：
https://plus.google.com/103234343779069345365/about

版权所有，严禁抄袭，欢迎转发。﻿
