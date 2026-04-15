+++
date = "2019-11-15T23:21:00+08:00"
draft = false
title = "crosscompiling for openwrt with nim"
tags = ["nim","openwrt"]

+++

### 缘来
nim语言([nimlang](https://nim-lang.org/))最近更新到1.02版本了，感觉还不错，正好最近在玩OpenWRT，记录一下在Linux下交叉编译mips架构的OpenWRT系统下的可执行程序步骤。

<!-- more -->
---
### Debian系统下下载nim语言
```bash
sh -c 'printf "deb http://ftp.cn.debian.org/debian/ sid main" > /etc/apt/sources.list.d/sid.list'
apt update
apt -t sid install nim -y
```
### Debian系统下安装gcc等编译环境
```bash
apt-get install build-essential
```

### 下载对应架构的OpenWRT编译工具链toolchain
```bash
wget https://archive.openwrt.org/releases/18.06.4/targets/ramips/mt7621/openwrt-sdk-18.06.4-ramips-mt7621_gcc-7.3.0_musl.Linux-x86_64.tar.xz
tar xvf openwrt-sdk-18.06.4-ramips-mt7621_gcc-7.3.0_musl.Linux-x86_64.tar.xz
rm openwrt-sdk-18.06.4-ramips-mt7621_gcc-7.3.0_musl.Linux-x86_64.tar.xz
```


### 设置环境并进行编译
```bash
export PATH=/root/openwrt-sdk-18.06.4-ramips-mt7621_gcc-7.3.0_musl.Linux-x86_64/staging_dir/toolchain-mipsel_24kc_gcc-7.3.0_musl/bin/:$PATH
nim -d:release --opt=size -d:ssl --cpu:mipsel --os:linux --gcc.exe:mipsel-openwrt-linux-gcc --gcc.linkerexe:mipsel-openwrt-linux-gcc c test.nim
```

### 结

就这样，就可以生成对应路由器下的可执行文件啦~