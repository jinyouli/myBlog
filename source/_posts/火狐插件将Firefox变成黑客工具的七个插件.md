---
title: '火狐插件将Firefox变成黑客工具的七个插件 '
date: 2017-10-12 09:46:57
categories:
tags:
---
目前很多插件不支持 Firefox 3.5 哦<!-- more -->
1. Add N Edit Cookies 查看和修改本地的Cookie，Cookie欺骗必备。
下载：http://code.google.com/p/editcookie/downloads/list


2. User Agent Switcher 修改浏览器的User Agent，可以用来XSS。
下载：https://addons.mozilla.org/zh-CN/firefox/addon/59


3. RefControl 修改Referer引用，也可以用来XSS或者突破一些防盗链。
下载：https://addons.mozilla.org/zh-CN/firefox/addon/953


4.Live HTTP Headers 记录本地的Get和Post数据，并可修改数据后重新提交。
下载：https://addons.mozilla.org/zh-CN/firefox/addon/3829


5. Poster   用来Post和Get数据。
下载：https://addons.mozilla.org/fr/firefox/addon/2691


6. HackBar 小工具包，包含一些常用的工具。(SQL injection,XSS,加密等)
下载：http://devels-playground.blogspot.com/2008/07/new-hackbar-132.html


7. XSS-Me &SQL Inject-Me&Access-Me 分别用来检测XSS，SQL Inject和Access缺陷。
下载：http://securitycompass.com/exploitme.shtml


怎么忘记了最牛逼的firebug啊
https://addons.mozilla.org/firefox/addon/1843


parosproxy

http://www.parosproxy.org/index.shtml


fiddler

http://www.fiddler2.com/Fiddler/help/video/default.asp

httpfox，httpwatch
这个也相当不错

---------------------------------------------------------------------------

IE类的HTTP工具有

tamperIE IEwatch IE headers

firefox的扩展有

LiveHttpheaders

tamperdata

modify headers

HTTP代理有

paros代理

webscarab代理

fiddler

watchfire powertools

还有牛多，以后更新！



---------------------------------------------------------------------------

google chromeProxy Switchy! （proxy你懂的）

https://chrome.google.com/extensions/detail/caehdcpeofiiigpdhbabniblemipncjj

Ultimate Chrome Flag
可以显示网站服务器所在地、IP、PR值、ALEXA、WOT评分(牛淫必备)

https://chrome.google.com/extensions/detail/dbpojpfdiliekbbiplijcphappgcgjfn

Pendule
方便的查看网页中的CSS、JS、IMAGES、表单等信息

https://chrome.google.com/extensions/detail/gbkffbkamcejhkcaocmkdeiiccpmjfdi


firefox
1. Add N Edit Cookies 查看和修改本地的Cookie，Cookie欺骗必备。

https://addons.mozilla.org/zh-CN/firefox/search?q=Add+N+Edit+Cookies&cat=all

2. User Agent Switcher 修改浏览器的User Agent，可以用来XSS。

https://addons.mozilla.org/zh-CN/firefox/addon/59

3. RefControl 修改Referer引用，也可以用来XSS或者突破一些防盗链。

https://addons.mozilla.org/zh-CN/firefox/addon/953

4.Live HTTP Headers 记录本地的Get和Post数据，并可修改数据后重新提交。

https://addons.mozilla.org/zh-CN/firefox/addon/3829

5. Poster 用来Post和Get数据。

https://addons.mozilla.org/fr/firefox/addon/2691

6. HackBar 小工具包，包含一些常用的工具。(SQL injection,XSS,加密等)(手工注入必备)

https://addons.mozilla.org/zh-CN/firefox/downloads/latest/3899/addon-3899-latest.xpi?src=addondetail

7. XSS-Me &SQL Inject-Me&Access-Me 分别用来检测XSS，SQL Inject和Access缺陷。

https://addons.mozilla.org/zh-CN/firefox/search?q=XSS-Me&cat=all

http://securitycompass.com/exploitme.shtml

8.Firebug 1.5.3 可以直接查看源码，相当与dreamweaver的拆分功能

https://addons.mozilla.org/firefox/addon/1843

9.HostSpy 旁注必备（使用方法http://www.daokers.com/article/original/618.htm）

https://addons.mozilla.org/zh-cn/firefox/addon/8946/



哪个就写哪个吧.

Tamperdata
用于查看和修改HTTP/ HTTPS的头和POST参数，跟踪HTTP请求和响应并记时,甚至修改网页应用程序。官方最近有个提示,谷歌的网络加速器(Google Web Accelerator)是无法与它兼容的，会导致浏览器崩溃.官方下载

FireSheep
非常优秀的一个Firefox扩展，用于HTTP会话劫持攻击.但不适用于火狐4.XBETA版本 http://codebutler.github.com/firesheep

Groundspeed
用于更改表格和表单元素，比如编辑隐藏字段的内容，取消文本字段的大小和长度的限制,可改变提交目标，可删除或编辑的JavaScript事件处理程序，避免客户端验证。其实有还有一个名字很高调的插件SQL Injection，但仅限于Firefox 3.0 – 3.6a1pre版本 官方下载

YSlow
yahoo开发的一个页面评分插件.通过分析网页性能和页面组件并对如何提高性能作出建意.这里顺便找到一总结YSlow的功能梗概，如果之前没了解，可以看看大概.YSlow跌评分规则 YSlow官方下载

Firecookie
属于Firebug扩展，通过它来查看和管理浏览器的cookies.类似插件还有https://addons.mozilla.org/zh-CN,

https://addons.mozilla.org/zh-CN … edit-cookies-13793/等等.顺便说下，在谷歌浏览器里也有几个仅适用于chrome的cookies插件，灰常强大,可以轻易吃到某些国际知名上传空间的饼干.：D 下载

View Dependencies
页面的图片，样式表，脚本，文件，每个服务器种类，页面数量可以一览无遗.一丝不挂的等着大牛们开垦http://mozilla.queze.net/

POster
简单实用.设置内容类型，通过HTTP请求，与Web服务交互和检查结果….http://code.google.com/p/poster-extension/

HackBar
名字太招摇，做人要低调，谁用谁知道 http://lmgtfy.com/?q=HackBar
突然想起来，刚才看那个发火狐插件哥们的帖子，流氓头子，就是整天带一个头盔那个，Admin回复说“之前有一个合集，不记得叫什么名字了”
现在这一刻我很崩溃的说，我也记得，但是写了一半才想起来….
叫做Ashiyane hacking add-ones
其中包括了13个精选并且与时俱进的火狐插件.历史惊人的巧合他竟然和我一样也选全适用于3.65以上版本……我刚才看了下，上面有几个和他没写，因为有些是4.0版本才有的.总算还有点价值.
过早结束….
顺便一下，Ashiyane hacking add-ones里面有一个user agent switcher如遇到ClickstreamFilter.java过滤客户端的每个请求，通过request获取User-Agent信息，来判断来源。这个插件主要功能就是在Firefox中切换User-Agent请求头消息。很好用.但是原本自带的User-Agent选择不多，需要options里自己增加.或者试试我这个经编好的list,XML格式，下载后在option里导入覆盖即可.http://depositfiles.com/files/qb852m8zf


