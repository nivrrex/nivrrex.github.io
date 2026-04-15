+++
date = "2014-07-08T21:27:40+08:00"
draft = false
title = "fanqiang with openwrt+vps"
tags = ["internet","fanqiang","openwrt","vps"]

+++

#### 来由
在上一篇[《fanqiang with vpn+chnroutes》](http://nivrrex.github.io/2014/06/28/fanqiang-with-vpn+chnroutes)中，通过免费的vpn和chnroutes，已经可以成功的翻墙了，后来觉得免费VPN不稳定，又买了一个月的付费vpn；再后来，觉得付费vpn不是共享，服务器上的多了，就又要换服务器，不爽；于是就决定，找个国外的vps主机，自己独享fq带宽！
<!-- more -->

---

#### 找合适的VPS
由于一个人用，不需要太好的，稳定，延时一般就OK，最好比较实惠。
开始时选择了年付$3.99的搬瓦工bandwagonhost（太便宜了啊），但是ping包时，发现丢包不是一般的多，遂放弃
又找到了[RamNode](http://www.ramnode.com)，有个年付$12的套餐，而且，然后用5.8折的优惠码，一年不到100￥，还是可以接受的，遂成交。
它最大的好处就是，丢包低于1%，而且有西雅图这种西海岸的机房，对于大陆来说，延时200ms左右，感觉还不错。
由于RamNode只支持PayPal，所以最终也了解了PayPal支持信用卡或者62开头的银联卡（开通网上银行），即可以支付了~

#### 使用VPS架设VPN
这个就不赘述了，网上一搜一大堆，我选择的操作系统是Debian：

* [乔大海的《BuyVM OpenVZ VPS Debian安装PPTP VPN》](http://qiaodahai.com/buyvm-openvz-vps-debian-pptp-vpn.html)
* [郭高兴的《VPS上安装PPTP（Xen+OpenVZ）完整教程》](http://hello.guo.biz/story/install-pptp-on-vps)
* [howtogeek的《How to Setup a VPN (PPTP) Server on Debian Linux》](http://www.howtogeek.com/51237/setting-up-a-vpn-pptp-server-on-debian/)
* [波斯马的《Linux（VPS+Debian）搭建配置VPN（PPTP）服务》](http://blog.bossma.cn/server/linux-vps-debian-vpn-server-pptp/)
在设置完VPN后，要注意MTU值的问题，否则部分网页打开的很慢或有问题：

* [永远的UNIX的《Fedora core 2下建立Poptop服务器以及常见问题》](http://fanqiang.chinaunix.net/app/other/2005-09-13/3655.shtml)

#### 路由器+VPN
###### 淘OpenWrt路由器
可以独享VPN上网后，还是不满足啊，因为家里2台电脑，手机还想无线上网，一台一台指定好费事啊
上一篇[《fanqiang with vpn+chnroutes》](http://nivrrex.github.io/2014/06/28/fanqiang-with-vpn+chnroutes)，最后提到，可以用开源的路由器固件，搞定路由器上VPN的问题。
Google了很长时间，锁定了OpenWrt，路由器选择淘宝上别人刷好的[Huawei HG255d](http://s8.taobao.com/search?spm=a230z.1.0.0.NCbfUi&promote=0&sort=sale-desc&initiative_id=staobaoz_20140708&q=hg255d&pid=mm_10062864_0_0&mode=63#J_relative)，基本在￥40-￥60之间。CPU为Ralink RT3052，FLASH容量为16MB，内存容量为32MB DDR。我淘的是硬改内存64MB+散热片，刷了不死uboot的，毕竟没那么多精力折腾。
###### 刷新最新OpenWrt固件
买回来后，淘宝店主刷新的是比较老的OpenWrt-DreamBox 20120828版本，对应的OpenWrt-DreamBox的源软件包地址已经改变，没法下载新软件，就开始折腾，找了半天，由于DreamBox版本比较老，所以换成了比较新的PandoraBox版本，可以到http://downloads.openwrt.org.cn/PandoraBox/HuaWei_HG255D/ 下载编译的最新固件，我选择的是最新的20140505版。
不得不说，有了uboot，可以一键刷机，还是比较放心的进行折腾的。在找到目前的固件前，刷新了不少版本，但都没啥问题。
#### 设置路由器+VPN+chnroutes静态路由
这个根据各人情况都不相同，总而言之，最终搞定。
###### 存在问题
一个是VPN拨号后，需要手动添加一下静态路由，不过是自己的VPS架设的VPN，想待多久就待多久，除了掉线，一般也不会下线滴。
另一个是，可能淘的Huawei HG255d有问题，路由器在对文件进行操作时，折腾一段时间后，会出现"input/output error"问题，怀疑是硬改导致的？
解决方案就是路由器关电重启解决，不过除了装软件，折腾东东，一般不影响使用，所以也不准备找淘宝店主问这个问题了。

---
总之，可以开始开心快乐的自由、随意的访问Internet了，so happy~
