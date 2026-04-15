+++
date = "2019-11-15T23:29:00+08:00"
draft = false
title = "crosscompiling for openwrt with golang"
tags = ["golang","openwrt"]

+++

### 缘来
实验了下nim语言([nimlang](https://nim-lang.org/))在OpenWRT系统下可执行程序的交叉编译，也想实验一下Golang的交叉编译，发现更方便。

<!-- more -->
---
### Golang支持的交叉编译平台
```bash
amd64 (also known as x86-64)
A mature implementation.

386 (x86 or x86-32)
Comparable to the amd64 port.

arm (ARM)
Supports Linux, FreeBSD, NetBSD, OpenBSD and Darwin binaries. Less widely used than the other ports.

arm64 (AArch64)
Supports Linux and Darwin binaries. New in 1.5 and not as well exercised as other ports.

ppc64, ppc64le (64-bit PowerPC big- and little-endian)
Supports Linux binaries. New in 1.5 and not as well exercised as other ports.

mips, mipsle (32-bit MIPS big- and little-endian)
Supports Linux binaries. New in 1.8 and not as well exercised as other ports.

mips64, mips64le (64-bit MIPS big- and little-endian)
Supports Linux binaries. New in 1.6 and not as well exercised as other ports.

s390x (IBM System z)
Supports Linux binaries. New in 1.7 and not as well exercised as other ports.

wasm (WebAssembly)
Targets the WebAssembly platform. New in 1.11 and not as well exercised as other ports.
```

### Golang针对mips系统的交叉编译
```bash
GOOS=linux GOARCH=mips go build -ldflags="-s -w" test.go
```
直接指定目标系统和CPU架构，就直接可以编译生成对应平台下的可执行文件

在mips架构下，如OpenWRT系统，文件执行时出现"Illegal instruction"错误，注意加上GOMIPS=softfloat变量

```bash
GOOS=linux GOARCH=mips GOMIPS=softfloat go build -ldflags="-s -w" test.go
```

### 结

就这样，golang可以更简单的直接生成对应系统和CPU架构下的可执行文件~