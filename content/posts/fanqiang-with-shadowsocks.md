+++
date = "2015-07-24T12:57:00+08:00"
draft = false
title = "fanqiang with shadowsocks"
tags = ["internet","fanqiang","vps","shadowsocks"]

+++

#### 来由
已经一年多没有更新这个blog了，现在来写点什么吧。
这一年来，没怎么做这方面的东西，fanqiang也主要是用VPS架设的PPTP VPN，速度一般，但是可以上很多网站啦。前一阵子，把RamNode上的VPS重新续费，然后购入了NETGEAR的WNDR4300路由器，刷了最新版本的14.07的OpenWRT在用。中间没有怎么折腾，基本够用。

最近突然想折腾了下，发现用Shadowsocks进行fanqiang比较快，下边就把经过写写吧：

<!-- more -->

---
#### VPS架设Shadowsocks
###### 选择Shadowsocks版本
Showsocks有好几种版本，Python, Golang, Nodejs, libev等，具体可以去[官网](http://shadowsocks.org/en/download/servers.html)看一下
因为说libev版本的占用内存较小，方便在我的VPS上部署，所以我选择了[shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev)

###### 编译Shadowsocks
我是直接通过git在官网上下载最新的包在Debian的VPS上进行编译的，具体如下：

```bash
git clone https://github.com/shadowsocks/shadowsocks-libev.git
cd shadowsocks-libev
sudo apt-get install build-essential autoconf libtool libssl-dev
./configure && make
make install
```

###### 配置Shadowsocks服务器端

```bash
vi /etc/shadowsocks-libev/config.json
```

具体格式如下：
```bash
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_port":1080,
    "password":"barfoo!",
    "timeout":600,
    "method":"table"
}
```

* server: 你的Shadowsocks服务器的IP地址 (IPv4/IPv6).
* server_port: 你的Shadowsocks服务器端口.
* local_port: 本地端口.
* password: 你的Shadowsocks服务器的账号密码.
* method: 你的Shadowsocks服务器采用的加密方式, "bf-cfb" , "aes-256-cfb" , "des-cfb" ,"rc4" , "table" 等等，建议使用"aes-256-cfb"

```bash
vi /etc/rc.local
```

添加以下自启动命令
```bash
/usr/bin/ss-server -c /etc/shadowsocks-libev/config.json
```
保存退出即可

如果希望加入udp转发功能(enable udprelay mode)，用以搭建DNS转发线路，启动时需要加上-u参数，似乎目前只有shadowsocks-libev有该功能
即添加以下自启动命令

```bash
/usr/bin/ss-server -c /etc/shadowsocks-libev/config.json -u
```
保存退出即可

由于shadowsocks-libev不支持多端口，多用户，如果多人使用的话，需开多个实例，每一个config.json使用不同端口、密码即可
官方示例如下：

```bash
ss-server -c config1.json -f pid1
ss-server -c config2.json -f pid2
ss-server -c config3.json -f pid3
```

###### 配置Shadowsocks客户端
可以直接从官网上推荐的各平台[clients](http://shadowsocks.org/en/download/clients.html)选择自己需要的客户端
我选择的是[shadowsocks-csharp](https://github.com/shadowsocks/shadowsocks-csharp)
最新的release可以在[这里](https://github.com/shadowsocks/shadowsocks-csharp/releases)下载
最新的客户端可以通过PAC文件，对GFWList里面的域名进行自动代理，OYE~

#### 路由器折腾Shadowsocks及自动fanqiang
好吧，本来希望在OpenWRT上折腾好，所有PC、手机可以直接自动科学上网的，但是一直没有试验成功。

* 一是：对iptables一直心怀敬意，但是折腾的太少，很多东西还不了解
* 二是：中国电信和GFW的合作(原谅我用这个词吧)太好了－_－||, 电信的宽带，一个IP地址连接我的VPS PPTP时间长了后，丢包就开始严重起来了，不得已需要重新PPPOE拨号连接，结果是换了一个地址，就没有丢包了呢－_－||。
* 三是：网上有说路由器的性能和内存受限，大流量后，SS有可能会崩溃，这样还要做个守护进程一直看着，随时重新启动，有点麻烦
* 四是：我确实没有折腾成功－_－||。。而且还把路由器折腾快瘫了
所以不想在路由器上折腾过多了，太麻烦了。。。

###### **PS:**

* 1、目前我的WNDR4300刷新的是Gargoyle(石像鬼)1.7.2固件，是恩山论坛的大拿编译的，[自译4300/3700/AR石像鬼+luci面板+over>90+ss+多拨+迅雷+脱机+广告+13信道+vpn集成](http://www.right.com.cn/forum/thread-157953-1-2.html)，我没有用他的集成包，而是用了精简版（支持overylay大于90M，集成石像鬼全部官方插件）（PS：换了这个固件后，感觉到VPS的丢包少了一些呢，不知道是不是只是感觉原因－_－||。。）
* 2、原先的华为HG255D刷新的是PandoraBox，[PandoraBox HG255D更新支持HWNAT硬件转发 最后的疯狂](http://www.right.com.cn/forum/thread-169216-1-3.html)，主要是解决了长期以来flash读写出错的问题和增加了硬件转发HWNAT功能，最新版本可以在[这里](http://downloads.openwrt.org.cn/PandoraBox/HuaWei_HG255D/firmware/stable/)下载
* 3、有想继续折腾OpenWRT+Shadowsocks的，可以自行下载SS的OpenWRT预编译包折腾，在[这里](http://sourceforge.net/projects/openwrt-dist/files/shadowsocks-libev/)下载


#### 最终解决方案
就是简单的VPS+Shadowsocks桌面客户端，随时更新GFWList的PAC文件，出问题也就是一台机子的事情。手机想科学上网时，通过原先的PPTP VPN即可

---
#### 总结
目前的方案是简单为主，不折腾，合适的设备做合适的事情，半自动也有半自动的好处。折腾的时间去做点其它事情好了~
