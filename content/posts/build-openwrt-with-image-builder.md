+++
date = "2014-07-17T22:17:11+08:00"
draft = false
title = "build openwrt with image builder"
tags = ["openwrt"]

+++


#### 来由
这几天在折腾OpenWrt，前文[fanqiang with openwrt+vps](http://nivrrex.github.io/2014/07/08/fanqiang-with-openwrt+vps/)里说过，	我用的是PandoraBox最新编译的版本，但是其内置的[packages](http://downloads.openwrt.org.cn/PandoraBox/ralink/rt305x/packages/)里面很多软件没有编译，如Python等。而直接换用官方的[packages](http://downloads.openwrt.org/attitude_adjustment/12.09/ramips/rt305x/packages/)，会提示兼容性问题，无法直接使用。
从网络Google了一下，可以两个同时使用。
<!-- more -->

```bash
src/gz attitude_adjustment http://downloads.openwrt.org/attitude_adjustment/12.09/ramips/rt305x/packages
src/gz pandorabox http://downloads.openwrt.org.cn/PandoraBox/ralink/rt305x/packages

dest root /
dest ram /tmp
lists_dir ext /var/opkg-lists
option overlay_root /overlay

arch all 100
arch ralink 200
arch ramips 300
arch ramips_24kec 400
```

这样就解决了软件兼容性的问题，但是前文[fanqiang with openwrt+vps](http://nivrrex.github.io/2014/07/08/fanqiang-with-openwrt+vps/)里面提到的"input/output error"问题仍然没有解决。遂升起了自己编译的念头。

#### 源代码编译
要从源代码编译OpenWrt，需要有Linux环境，主要好像是因为Windows下大小写不区分，可能导致异常问题。
于是选了Debian系统，主要是玩OpenWrt时，又淘了一个树莓派（Raspberry Pi）玩，而它的默认系统就是基于Debian，同时最早玩的Ubuntu也是基于Debian的，买的VPS也是基于Debian系统的，so...
于是到达[官网](https://www.debian.org/CD/torrent-cd/)选择i386架构、xfce界面的debian-7.6.0-i386-xfce-CD-1.iso。
通过UltraISO写入到U盘，然后安装到硬盘上，OK。
###### 编译源代码
这个痛苦的过程不想说了
主要是下载Debian上的编译支撑工具，然后通过以下说明进行编译

* [OpenWrt Buildroot - 安装](http://wiki.openwrt.org/zh-cn/doc/howto/buildroot.exigence)
* [OpenWrt Buildroot - 使用说明](http://wiki.openwrt.org/zh-cn/doc/howto/build)
最终结果是，我从下午编译到晚上，还没有编译完成，于是让其继续。第二天起来后，发现网络断了，编译失败。

#### Image Builder编译
从头开始的自行编译实在hold不住，于是就考虑其它方式，由于OpenWrt的编译选项中，可以将结果进行打包给其它人使用，官网上也有相应的打包的tar.gz2文件可以下载，也就是Image Builder的形式。考虑用它实现，说明如下：

*[Image Generator (Image Builder)](http://wiki.openwrt.org/zh-cn/doc/howto/obtain.firmware.generate)
下载、解压缩，然后看需要的包，我是将PandoraBox里面所有的包都包含了，然后加上了Python、DNS工具包等。
命令如下：

```bash
make image PROFILE=HG255D PACKAGES="6in4 6rd 6to4 aria2 arptables badblocks base-files bind-dig bind-libs blkid block-mount busybox cfdisk chat comgt coreutils curl ddns-scripts dnsmasq dnsmasq-full dropbear e2fsprogs ebtables ebtables-utils etherwake fdisk firewall hd-idle ip iptables iptables-mod-account iptables-mod-chaos iptables-mod-condition iptables-mod-conntrack-extra iptables-mod-extra iptables-mod-filter iptables-mod-geoip iptables-mod-hashlimit iptables-mod-ipmark iptables-mod-ipopt iptables-mod-ipp2p iptables-mod-iprange iptables-mod-led iptables-mod-length2 iptables-mod-logmark iptables-mod-nat-extra iptables-mod-sysrq iptables-mod-tee iptables-mod-u32 iptables-mod-ulog iwinfo jshn kernel kmod-arptables kmod-button-hotplug kmod-crypto-arc4 kmod-crypto-ecb kmod-crypto-hash kmod-crypto-sha1 kmod-ebtables kmod-fs-exportfs kmod-fs-ext4 kmod-fs-nfs kmod-fs-nfs-common kmod-fs-ntfs kmod-fs-vfat kmod-fs-xfs kmod-fuse kmod-gpio-dev kmod-gre kmod-ifb kmod-input-core kmod-input-gpio-keys-polled kmod-input-polldev kmod-ipt-account kmod-ipt-chaos kmod-ipt-compat-xtables kmod-ipt-condition kmod-ipt-conntrack kmod-ipt-conntrack-extra kmod-ipt-core kmod-ipt-delude kmod-ipt-filter kmod-ipt-geoip kmod-ipt-hashlimit kmod-ipt-ipmark kmod-ipt-ipopt kmod-ipt-ipp2p kmod-ipt-iprange kmod-ipt-led kmod-ipt-length2 kmod-ipt-logmark kmod-ipt-nat kmod-ipt-nat-extra kmod-ipt-nathelper kmod-ipt-nathelper-extra kmod-ipt-sysrq kmod-ipt-tarpit kmod-ipt-tee kmod-ipt-u32 kmod-ipt-ulog kmod-iptunnel4 kmod-iptunnel6 kmod-ipv6 kmod-l2tp kmod-leds-gpio kmod-ledtrig-default-on kmod-ledtrig-gpio kmod-ledtrig-heartbeat kmod-ledtrig-morse kmod-ledtrig-netdev kmod-ledtrig-netfilter kmod-ledtrig-timer kmod-ledtrig-usbdev kmod-lib-crc-ccitt kmod-lib-crc16 kmod-lib-textsearch kmod-macvlan kmod-mppe kmod-nls-base kmod-nls-cp437 kmod-nls-iso8859-1 kmod-nls-utf8 kmod-ppp kmod-pppoe kmod-pppol2tp kmod-pppox kmod-pptp kmod-sched kmod-sched-core kmod-scsi-core kmod-scsi-generic kmod-sit kmod-tun kmod-usb-acm kmod-usb-core kmod-usb-ohci kmod-usb-printer kmod-usb-rt305x-dwc_otg kmod-usb-serial kmod-usb-serial-ftdi kmod-usb-serial-option kmod-usb-serial-pl2303 kmod-usb-serial-qualcomm kmod-usb-serial-wwan kmod-usb-storage kmod-usb-storage-extras kmod-usb-uhci libblkid libblobmsg-json libc libcurl libdaemon libext2fs libffi libgcc libgd libip4tc libip6tc libiwinfo libiwinfo-lua libjpeg libjson-c liblua libncurses libnfnetlink libnl-tiny libopenssl libpcap libpng libpthread librt libstdcpp libubox libubus libubus-lua libuci libuci-lua libuuid libxml2 libxtables lua luci luci-app-commands luci-app-ddns luci-app-firewall luci-app-hd-idle luci-app-ntpc luci-app-p910nd luci-app-qos luci-app-radvd luci-app-samba luci-app-upnp luci-app-vnstat luci-app-wol luci-i18n-chinese luci-i18n-english luci-lib-nixio luci-proto-3g luci-proto-ppp mjpg-streamer ntpclient pdnsd ppp ppp-mod-pppoe ppp-mod-pptp python python-mini resolveip restorefactory ser2net transmission-daemon transmission-remote transmission-web uci uhttpd unbound vsftpd wget wireless-tools zlib"
```

结果提示出错，原因是，官网的打包是64bit的，而我安装的是i386架构的Debian。
OK，重新下载后安装，结果老版本的UltraISO无法读入AMD64的ISO包，仅有一个目录，400多KB大小，更新UltraISO到较新的9.5版本，问题解决。
重新安装Debian，配置编译工具，下载Image Builder包，重新编译，结果没有bin文件，Google后晓得，原来需要更改以下Image文件中的内容:

```bash
../OpenWrt-ImageBuilder-ramips_rt305x-for-linux-x86_64/target/linux/ramips/imag/Makefile
```

查找HG255D字符串，把对应的注释#符号删除，重新编译

```bash
#Image/Build/Profile/HG255D=$(call BuildFirmware/Default16M/$(1),$(1),hg255d,HG255D)
#	$(call Image/Build/Profile/HG255D,$(1))
```

OK，固件“.bin”文件产生了
写入到路由器中，可以正常的拨号上网了~

###### 遗留问题
trunk版本和RC1版本的OpenWrt预编译包，PPTP VPN无法正常拨通，提示错误，暂时解决不了，于是又退回PandoraBox版本了，而PandoraBox版本无法安装比较大的软件包，还是出现"input/output error"错误，使用jsff2make也没有用。
也不知道到底是HG255D的原因，还是OpenWrt的原因，不准备折腾这个了，计划在LAN中加入一个树莓派，然后用树莓派搞定我的特殊需求，HG255D只做路由器使用。先这样吧~
PS:最终方案是，HG255D解决DNS污染问题，本地电脑进行静态路由和VPN拨号。
折腾来折腾去啊~
