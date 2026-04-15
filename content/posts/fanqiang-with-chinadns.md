+++
date = "2015-07-26T12:54:59+08:00"
draft = false
title = "fanqiang with chinadns"
tags = ["internet","fanqiang","openwrt","chinadns"]

+++

#### 来由
用Opera等浏览器时，由于DNS被GFW污染了，所以fanqiang的有些不爽快，像Twitter.com和Facebook.com无法上，虽然IE等借助Shadowsocks可以直接上？所以想在OpenWRT上，把DNS污染问题彻底解决了。

<!-- more -->

---
#### Old解决方案 - FreeRouterV2
以前的解决方案是借用[FreeRouterV2](https://github.com/lifetyper/FreeRouter_V2)的部分iptables策略，针对被GFW污染的数据包进行丢弃，然后获得正常的数据包，具体原理作者详细的写了PDF文件进行说明，[如下](https://github.com/lifetyper/FreeRouter_V2/blob/master/FreeRouterV2_HandBook.pdf)

###### 安装
首先需要在OpenWRT上安装如下包：
```bash
opkg update
opkg install iptables-mod-filter iptables-mod-u32 kmod-ipt-filter kmod-ipt-u32
```
###### 配置
然后配置iptables的防火墙策略
```bash
vi /etc/firewall.user
```
添加如下命令
```bash
iptables -t mangle -I PREROUTING -p udp --sport 53 -m u32 --u32 "0&0x0F000000=0x05000000 && 0>>22&0x3C@8&0x810F=0x8000 && 0>>22&0x3C@12&0xFFFF=0x0000" -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|042442B2|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|0807C62D|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|1759053C|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|253D369E|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|2E52AE44|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|31027B38|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|364C8701|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|3B1803AD|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|402158A1|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|4021632F|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|4042A3FB|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|4168CAFC|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|41A0DB71|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|422DFCED|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|480ECD63|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|480ECD68|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|4A7D2766|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|4A7D2771|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|4A7D7F66|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|4A7D9B66|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|4D04075C|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|4E10310F|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|5D2E0859|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|76053106|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|80797E8B|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|9F6A794B|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|A9840D67|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|BC050460|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|BDA31105|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|C043C606|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|C504040C|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|CA6A0102|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|CAB50755|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|CB620741|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|CBA1E6AB|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|CF0C5862|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|D0381F2B|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|D1244921|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|D155E58A|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|D1913632|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|D1DC1EAE|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|D35E4293|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|D5A9FB23|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|D8DDBCB6|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|D8EAB30D|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|F3B9BB27|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|F9812E30|" --from 60 --to 180  -j DROP
iptables -t mangle -I PREROUTING -p udp --sport 53 -m string --algo bm --hex-string "|FD9D0EA5|" --from 60 --to 180  -j DROP
```
重启防火墙即可
```bash
/etc/init.d/firewall restart
```

#### ChinaDNS解决方案
以上的方案挺好，但是GFW的污染IP似乎有增多现象，所以想省心一点，正好shadowsocks作者[clowwindy](https://github.com/clowwindy)有一个针对DNS污染的作品[ChinaDNS](https://github.com/clowwindy/ChinaDNS)，就直接拿过来用了。
针对OpenWRT/Gargoyle，作者有直接编译完毕的ipk包，下载后安装到OpenWRT即可，地址[如下](https://github.com/clowwindy/ChinaDNS/releases)

###### 安装
```bash
opkg install ChinaDNS_1.x.x_ar71xx.ipk
/etc/init.d/chinadns start
/etc/init.d/chinadns enable
```

###### 配置
```bash
vi /etc/dnsmasq.conf
```
添加如下内容并保存退出
```bash
no-resolv
server=127.0.0.1#5353
```
重新启动
```bash
/etc/init.d/chinadns restart
/etc/init.d/dnsmasq restart
```
此时直接使用路由器IP做DNS服务器即可，也可以对chinadns做更进一步的配置
```bash
vi /etc/init.d/chinadns
```
具体参数如下
```bash
usage: chinadns [-h] [-l IPLIST_FILE] [-b BIND_ADDR] [-p BIND_PORT]
       [-c CHNROUTE_FILE] [-s DNS] [-v]
Forward DNS requests.

-h, --help            show this help message and exit
-l IPLIST_FILE        path to ip blacklist file
-c CHNROUTE_FILE      path to china route file
                      if not specified, CHNRoute will be turned off
-d                    enable bi-directional CHNRoute filter
-y                    delay time for suspects, default: 0.3
-b BIND_ADDR          address that listens, default: 127.0.0.1
-p BIND_PORT          port that listens, default: 53
-s DNS                DNS servers to use, default:
                      114.114.114.114,208.67.222.222:443,8.8.8.8
-m                    Using DNS compression pointer mutation
                      (backlist and delaying would be disabled)
-v                    verbose logging
```

#### SFTP登录
在将ipx文件上传到OpenWRT路由器上时，刚开始没有使用WinSCP，而且OpenWRT上也没有SFTP服务，所以是用Everything的HTTP服务器功能，然后在OpenWRT上用wget下载的。后来知道可以直接通过SFTP上传、下载文件后，就开始在OpenWRT上折腾了。
###### OpenWRT安装SFTP
安装及配置命令如下：

```bash
opkg update
opkg install vsftpd openssh-sftp-server
/etc/init.d/vsftpd enable
/etc/init.d/vsftpd start
```
###### md5sum mismatch 错误
其中opkg在线安装openssh-sftp-server时出现了md5sum mismatch，无法安装，网上Google了一下，表示可能是linux内核不匹配导致的，解决方法是下载到本地，直接安装即可
好吧，打开Everything的HTTP服务器，先下载到OpenWRT路由器上，然后手动安装成功。

---
#### 结
没有了DNS污染，所有被屏蔽的网站也可以通过Shadowsocks+GFWList PAC绕过去科学上网了，自由遨游Internet的感觉真好啊~ \^O^/
