+++
date = "2015-07-26T17:51:59+08:00"
draft = false
title = "fanqiang with dnsmasq"
tags = ["internet","fanqiang","vps","dnsmasq"]

+++

#### 来由
本来已经翻墙解决的差不多了，但是在下午下载东西时，发现频繁出现域名无法解析现象，最让人愤怒的是，竟然连www.symantec.com都解析不出来了，应该是GFW对DNS污染的封堵升级导致的吧，一直是DNS解析超时。so，兴起了在自己的VPS上架设一个DNS服务器，作为ChinaDNS的备用上游服务器用的念头，说干就干啊~
<!-- more -->

#### DNSmasq安装
我的系统是Debian，直接运行以下命令

```bash
sudo apt-get update
sudo apt-get install dnsmasq
```

#### DNSmasq配置
编辑/etc/dnsmasq.conf文件

```bash
vi /etc/dnsmasq.conf
```
修改以下参数

```bash
resolv-file=/etc/resolv.conf
strict-order

listen-address=xxx.xxx.xxx.xxx,127.0.0.1
port=443

cache-size=1024
log-queries
```
编辑/etc/resolv.conf文件，添加DNSmasq的上游服务器

```bash
nameserver 8.8.8.8
nameserver 208.67.222.222
```
将服务重启即可

```bash
/etc/init.d/dnsmasq restart
```

将DNSmasq加入启动项

```bash
sudo vi /etc/rc.local
```
将以下命令添加进去即可

```bash
/etc/init.d/dnsmasq start
```

#### DNS污染解决方案
[OpenWrt-dist](http://sourceforge.net/projects/openwrt-dist/)上面单门架设了一个页面，针对OpenWrt的fanqiang软件做了很多预编译工作。
其中针对防DNS劫持的解决方案单门有个Wiki页面，共计六种方案，具体可以参考一下，**[地址](http://sourceforge.net/p/openwrt-dist/wiki/DNS/)**

---
#### 结
这次终于应该没有问题了，www.symantec.com这些网站也不会出现DNS解析超时的问题，终于可以彻底好好科学上网学习啦~
