+++
date = "2018-07-09T20:12:00+08:00"
draft = false
title = "ubnt er-x use openwrt"
tags = ["ubnt","openwrt"]

+++



给UBNT的ER-X刷OpenWrt固件

### 缘
UBNT 的 ER-X 是个弱电箱的神器，设备很小，用的是 MT7621AT 。虽然没有无线，但是弱电箱里面无线信号本来就屏蔽的厉害，后期加装个 AP 就行了。

于是购置了一台 ER-X ，因为我目前的翻墙方式是 Shadowsocks + ChinaDNS + DNS Forwarder 方式，配置到 OpenWrt 路由器上实现的，具体步骤详见 https://cokebar.info/archives/664 。而UBNT的路由器系统 edgeRouter 还需要对以上的软件进行交叉编译，就不想费功夫了，直接刷成OpenWrt，用之前的方式翻墙就行了。而且 OpenWrt 的 18.06 版本，最新的 4.14 内核的 FLOW OFFLOAD ，也算支持 MT7621 的硬转发了，性能应该问题不大。

<!-- more -->

### 安装

#### 前期问题
刚开始安装时，因为 edgeRouter 系统不支持 OpenWrt 官方的 ubnt-erx-initramfs-kernel.bin，校验不通过，必须是类似 factory-initramfs.tar 的固件包才行。

于是到 http://bbs.ubnt.com.cn/forum.php?mod=viewthread&tid=15243 帖子中和国外的一个帖子中提到的 http://s000.tinyupload.com/index.php?file_id=07741512272586742124 ，分别下载了他们的 factory-initramfs.tar 类的固件，确实可以安装了，但是在用sysupgrade命令更新最新的OpenWrt系统时，出现了下面的问题：

```
Request timed out
```

而且这两个系统，都无法保存设置，安装新软件。差点以为要买个TTL线救回原先的系统了。

#### 恢复官方固件
后来在 http://bbs.ubnt.com.cn/forum.php?mod=viewthread&tid=19800 帖子里面，发现“4.刷回官方固件”以后的步骤，直接可以用。就用下边的方式，又还原成1.7系统了。下边记录一下：

```
##这一部分不是帖子的内容，是前期准备工作
#在PC上下载ER-X的官方固件包，解压上传至路由器 /root 目录下
wget https://dl.ubnt.com/firmwares/edgemax/v1.7.0/ER-e50.v1.7.0.4783374.tar
tar -xf ER-e50.v1.7.0.4783374.tar
rm ER-e50.v1.7.0.4783374.tar
#解压后应该有6个文件 compat  squashfs.tmp  squashfs.tmp.md5  version.tmp  vmlinux.tmp  vmlinux.tmp.md5
scp -P 22 compat squashfs.tmp squashfs.tmp.md5 version.tmp vmlinux.tmp vmlinux.tmp.md5 root@192.168.1.1:/root


##以下是帖子中提到各个步骤
#登录路由器后，还原ubnt系统
cd /root
ubidetach -p /dev/mtd5
ubiformat /dev/mtd5
ubiattach -p /dev/mtd5
ubimkvol /dev/ubi0 --vol_id=0 --lebs=1925 --name=troot
mount -o sync -t ubifs ubi0:troot /mnt/

cp version.tmp /mnt/version 
cp squashfs.tmp /mnt/squashfs.img
cp squashfs.tmp.md5 /mnt/squashfs.img.md5

dd if=vmlinux.tmp of=/dev/mtdblock3
dd if=vmlinux.tmp of=/dev/mtdblock4

reboot
```
正常重启后，应该就恢复成官方1.7系统了，然后注意把网线接入Eth0口，正常配置下 edgeRouter 系统就行了。

**注意**：目前实验的，似乎只有上边的两个装不了新软件的 OpenWrt 系统的可以刷回官方系统，OpenWrt 官方的系统刷机时，会出现以下错误（但是应该用TTL线还是可以刷回官方系统的，可以参考上边的帖子的前半部分内容）：

```
ubidetach: error!: cannot detach "/dev/mtd5"
           error 16 (Resource busy)
```

### 柳暗花明
刷会官方系统后，本来以为没办法了。后来搜索到一个网站 https://wiki.opennet-initiative.de/wiki/Ubiquiti_EdgeRouter ,似乎是一个在 OpenWrt 系统上叠加 OpenNet 安全网络的组织。网页上有详细的从官方系统刷新到 OpenWrt系统的说明，而且有对应的固件。

#### 安装initramfs-factory
按该网站的说明，首先从官方系统，刷新成OpenWrt系统，下面记录下具体的步骤

```
#PC上下载factory固件，并上传
wget http://downloads.opennet-initiative.de/ubnt/edgeos/openwrt-ramips-mt7621-ubnt-erx-initramfs-factory.tar
scp -P 22 openwrt-ramips-mt7621-ubnt-erx-initramfs-factory.tar ubnt@192.168.1.1:/tmp

#登录路由器，并添加OpenWrt系统
ssh -p 22 ubnt@192.168.1.1   #密码ubnt
cd /tmp
add system image openwrt-ramips-mt7621-ubnt-erx-initramfs-factory.tar
show system image
reboot 
```

正常情况下，重启后，将进入 OpenNet 的 OpenWrt 系统，然后可以刷新最新的 OpenWrt 系统（应该也可以刷新 OpenWrt 的官方系统，这里是更新成了 OpenNet 的系统）

#### 更新squashfs-sysupgrade

```
#PC上下载sysupgrade固件，并上传
wget http://downloads.opennet-initiative.de/openwrt/stable/latest/targets/ramips/mt7621/lede-0.5.4-2283-ramips-mt7621-ubnt-erx-squashfs-sysupgrade.tar
scp -P 22 lede-0.5.4-2283-ramips-mt7621-ubnt-erx-squashfs-sysupgrade.tar root@192.168.1.1:/tmp

#登录路由器，并更新OpenWrt系统
ssh -p 22 root@192.168.1.1
cd /tmp
sysupgrade lede-0.5.4-2283-ramips-mt7621-ubnt-erx-squashfs-sysupgrade.tar
```

sysupgrade 更新后会自动重启，结束后就是 OpenNet 的最新的 OpenWrt 系统了。我是在这个系统基础上，又更新成官方的 OpenWrt 系统了。

**注意**： OpenNet 的 OpenWrt 系统，网段是172.16.0.1/24 , 帐号root , 密码admin


### 结
至此，ER-X 刷新 OpenWrt 系统成功。（如果此时还想恢复官方系统，就必须通过TTL线了）

虽然 OpenWrt 对比 Ubnt 的 edgeRouter 系统可能性能有点弱势，但是比不了方便啊，而且支持 OpenWrt 的翻墙类软件还是比较丰富和更新及时的，就这样用啦~
