+++
date = "2020-08-30T09:19:10+08:00"
draft = false
title = "dlang with ldc in mipsel"

+++

# 简介
突然想用dlang语言玩玩，然后又希望能在路由器上使用(openwrt mipsel)，就实验了一下[Cross-compiling with LDC](https://wiki.dlang.org/Cross-compiling_with_LDC)，现在将具体过程记录一下。

# mipsel gcc
首先需要到openwrt下载路由器对应的gcc的Cross-Compiler，我用的是mt7621的路由器，并将其下载到了 /root/mipsel/ 目录下

```bash
cd /root/
wget https://downloads.openwrt.org/releases/19.07.3/targets/ramips/mt7621/openwrt-sdk-19.07.3-ramips-mt7621_gcc-7.5.0_musl.Linux-x86_64.tar.xz
tar xvf openwrt-sdk-19.07.3-ramips-mt7621_gcc-7.5.0_musl.Linux-x86_64.tar.xz
rm openwrt-sdk-19.07.3-ramips-mt7621_gcc-7.5.0_musl.Linux-x86_64.tar.xz
mkdir mipsel
mv openwrt-sdk-19.07.3-ramips-mt7621_gcc-7.5.0_musl.Linux-x86_64/* mipsel/
rm -r openwrt-sdk-19.07.3-ramips-mt7621_gcc-7.5.0_musl.Linux-x86_64
```

设置环境变量

```bash
cat << \EOF >> /etc/bash.bashrc
export PATH=/root/mipsel/staging_dir/toolchain-mipsel_24kc_gcc-7.5.0_musl/bin/:$PATH
export STAGING_DIR=/root/mipsel/staging_dir/:STAGING_DIR
alias strip-mipsel='mipsel-openwrt-linux-strip -s'
EOF
source /etc/bash.bashrc
```
如上，mipsel的gcc交叉编译环境就配置好了

# ldc 下载
到ldc官网下载最新版本的ldc，将其下载设置到 /root/ldc2/ 目录下，并设置path环境变量
```bash
wget https://github.com/ldc-developers/ldc/releases/download/v1.23.0/ldc2-1.23.0-linux-x86_64.tar.xz
tar xvf ldc2-1.23.0-linux-x86_64.tar.xz
rm ldc2-1.23.0-linux-x86_64.tar.xz
mv ldc2-1.23.0-linux-x86_64 ldc2

cat << \EOF >> /etc/bash.bashrc
export PATH=$PATH:/root/ldc2/bin
EOF
source /etc/bash.bashrc
```
# 编译mipsel的runtime
需要下载 cmake ninja-build 等工具支持编译，并将编译好的 .so .a 文件移动到/root/ldc2/ldc-runtime/mipsel目录下

```
apt install cmake ninja-build -y
cd /root
CC=mipsel-openwrt-linux-gcc ldc-build-runtime --ninja --dFlags="-w;-mtriple=mipsel-linux;-march=mipsel;-mcpu=mips32;--float-abi=soft" --cFlags="-march=mips32" --targetSystem="Linux;UNIX"
mkdir /root/ldc2/ldc-runtime/mipsel -p
mv ldc-build-runtime.tmp/lib/ /root/ldc2/ldc-runtime/mipsel
rm ldc-build-runtime.tmp/ -r
```

# 编译下载libexecinfo
如果此时直接进行编译，会出现 **undefined reference to `backtrace_symbols'**  错误，还需要编译libexecinfo库。

```bash
cd /root
wget https://github.com/mikroskeem/libexecinfo/archive/1.1-3.tar.gz
tar xvf 1.1-3.tar.gz
rm 1.1-3.tar.gz
cd libexecinfo-1.1-3
sed -i "s%CC=cc%CC=mipsel-openwrt-linux-gcc%g" Makefile 
sed -i "s%AR=ar%AR=mipsel-openwrt-linux-ar%g" Makefile 
make
```

编译后的库文件放置到 /root/ldc2/ldc-runtime/mipsel/lib/ 文件夹下，便于后续使用

```bash
mv libexecinfo.a /root/ldc2/ldc-runtime/mipsel/lib/
mv libexecinfo.so.1 /root/ldc2/ldc-runtime/mipsel/lib/
mv stacktraverse.So /root/ldc2/ldc-runtime/mipsel/lib/
mv execinfo.So /root/ldc2/ldc-runtime/mipsel/lib/
rm /root/libexecinfo-1.1-3/ -r
```

# 编译.d代码文件
简单的hello world代码

```d
import std.stdio;
void main()
{
    writeln("Hello, world! dlang in mipsel!");
}
```

编译至mipsel环境
```bash
ldc2 -mtriple=mipsel-linux -march=mipsel -mcpu=mips32 --float-abi=soft -gcc=mipsel-openwrt-linux-gcc --linker=bfd -L-L/root/ldc2/ldc-runtime/mipsel/lib -defaultlib=phobos2-ldc,druntime-ldc --platformlib=execinfo hello.d
```

# 设置alias

```bash
cat << \EOF >> /etc/bash.bashrc
alias ldc2-mipsel="ldc2 -mtriple=mipsel-linux -march=mipsel -mcpu=mips32 --float-abi=soft --release -Os -gcc=mipsel-openwrt-linux-gcc --linker=bfd -L-L/root/ldc2/ldc-runtime/mipsel/lib -defaultlib=phobos2-ldc,druntime-ldc --platformlib=execinfo"
EOF
source /etc/bash.bashrc
```

# 结
至此，dlang在linux环境下，编译mipsel架构的编译环境就搭配好了，平时可以用dlang针对mipsel的openwrt路由器写点小工具啦~

