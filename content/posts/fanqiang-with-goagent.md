+++
date = "2014-06-25T21:45:26+08:00"
draft = false
title = "fanqiang with goagent"
tags = ["internet","fanqiang","goagent"]

+++

#### 来由
最近Google被屏蔽的很厉害，想查个东西都要翻来覆去的，灰常麻烦。
刚开始以为一段时间就OK了，就用了临时性的解决方案。
<!-- more -->
比如：

* 使用[Bing](http://www.bing.com/?mkt=zh-CN)代替。 *搜索效果相比Google还有差距*
* 使用[StartPage](https://www.startpage.com/)代替。 *相比原版，存在部分结果搜索不到的问题*
* 直接用[Google IP](https://github.com/justjavac/Google-IPs/blob/master/README.md)进行搜索。 *可以使用的IP被飞快的封锁*
* 使用网上的免翻墙镜像网站[greatfire](https://github.com/greatfire/wiki)。 *是个不错的解决方案，但是怕被封锁后还要重新设置*
* 使用网上的免费VPN[免费VPN资源](http://ilvpn.com/free-vpn/)。 *免费的要么限制流量，要么限制时间，而且免费的基本上速度比较慢*
* 使用google的GAE平台的[goagent](https://code.google.com/p/goagent/)。 *强烈推荐*

---

#### goagent设置
这个网上教程很多，随便搜索都有好几个，如：

* [猫理会的goagent教程详细版](http://maolihui.com/goagent-detail.html)
* [软矿的GoAgent图文设置教程](https://www.x-berry.com/goagent/)
* [海盗船博客的2014最新翻墙方法之goagent翻墙教程](http://blog.4a8a.com/post-133.html)

---

#### 软件设置
在搭建好goagent后，就是如何使用它配合浏览器上网了
我常用的浏览器有IE、猎豹、Opera
为统一解决，我的方法是：

* 1、猎豹浏览器中安装插件SwitchySharp。然后建立规则，默认直接连接，然后设置需要翻墙的网站清单。设置成功后，导出PAC文件。
* 2、IE中设置代理服务器，勾选代理服务器，“127.0.0.1 8087”。将“使用自动配置脚本”勾选，然后按格式填入“file://C:\GoAgent\SwitchyPac.pac”。如果有代理后出问题的网站，选“高级”，将对应网站填入“例外”区域内。
* 3、Opera浏览器：Tools -> Preferences -> Advanced -> Network -> Proxy Servers -> "Use automatic proxy configuration"，填写“file://C:\GoAgent\SwitchyPac.pac”。
* 4、IE和Opera都需要安装goagent的证书，否则访问时会出错。
* 5、可以在[这里](https://code.google.com/p/autoproxy-gfwlist/)订阅部分数据。

---

#### 结
堵不如疏，闭关锁国只能让自己更落伍。
人民总有办法得到自己想知道的东西的，而且通过这种方式得到的信息，反而会倾向于相信。
大多数人使用Google只是想查找自己想要的知识。
