+++
date = "2015-07-27T22:36:17+08:00"
draft = false
title = "update debian linux image with backports"
tags = ["debian","vps"]

+++

#### 来由
因为Shadowsocks-libev的部分功能需要linux高版本的内核才能支持。兴致所起，准备更新一下VPS上的Debian内核版本。

<!-- more -->

---
#### 如何查看Linux版本
共有5种方式，具体如下

```bash
cat /etc/*-release
lsb_release -a
cat /proc/version
uname -a  #uname -mrs
cat /etc/issue
```

我的Debian更新过，显示是

```bash
Debian GNU/Linux 8.1 (jessie)
```
但是，内核为什么还是

```bash
Linux 2.6.32-042stab093.4 i686
```
好吧，更新，更新！

#### 使用Backports源
对于Debian来说，其稳定版的软件虽然稳定，但是都好OLD啊，但是好在它有个Backports项目

其[官方Wiki](https://wiki.debian.org/zh_CN/Backports)上面说

Backports是从testing版本 （大部分）和 unstable版本 (可能性极小,例如：安全更新等)重新编译的软件包,因此他们在稳定的debian发行版中不需要新的库就可以运行（无论如何，这是可能的）。推荐你挑选一个能够满足你需求的单个的backports软件源，不要使用所有可用的backports源。

#### 开始更新
###### 更新软件源
首先在apt的sources.list文件中添加Backports源

```bash
vi /etc/apt/sources.list
```
添加如下内容

```bash
deb http://ftp.debian.org/debian/ jessie-backports main
deb-src http://ftp.debian.org/debian/ jessie-backports main
```
更新数据

```bash
sudo apt-get update
#sudo apt-get -t jessie-backports upgrade
```
###### 搜索并安装linux-image文件
安装aptitude，以便搜索最新的linux-image文件

```bash
apt-get install aptitude
aptitude search kernel-image
```
根据搜索出来的内核文件，进行安装，我的是

```bash
sudo apt-get -t jessie-backports install linux-image-3.16.0-4-686-pae
#sudo apt-get -t jessie-backports install linux-image-686-pae
```
###### 显示已安装的linux-image内核版本
显示已经安装的linux-image内核版本

```bash
dpkg --get-selections |grep linux-image
```
###### 删除旧内核
如果要删除旧内核，运行以下命令

```bash
sudo apt-get remove linux-image-3.16.0-4-686-pae
```
###### 更新grub
在/boot/grub/grub.cfg中查看是否已经有新内核3.16，有的话，就已经OK。否则，自己手动更新grub。
sudo update-grub

#### 安装结果
我兴高采烈的在我的RamNode的VPS折腾了半天后，重新Reboot后发现，内核为什么还是2.6.32!!!

```bash
Linux 2.6.32-042stab093.4 i686
```
###### Google原因
OpenVZ的内核和 Host的内核是一致的
也就是说，由于我的VPS是OpenVZ的，所以我自己更新linux-image的版本是没有用的。。。

#### 结
伤心了 -__- ，于是写下这篇小结，算是个记录。顺便把家里的Debian主机更新了下内核，3.16更新到3.16，小版本也要更新！。。。
