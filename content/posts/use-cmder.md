+++
date = "2018-06-04T14:24:00+08:00"
draft = false
title = "use cmder"
tags = ["soft"]

+++

### 缘来
Windows下的命令行一直没有什么进化，命令不多，处理也很不方便，比较其Linux的shell，差距比较大。所以网上一直也有cygwin和msys这种，将Linux的命令移植到Windows下使用的工具集。但虽然命令有了，但命令行自身的一些劣势，还是没有解决。最近，从网上看到了[cmder](http://cmder.net/)这个很方便的替代windows自带命令行的好工具，记录一下。

<!-- more -->
---
### 介绍
cmder由3部分组成：
##### [ConEmu](https://conemu.github.io/en/)
ConEmu 是一套 Windows 控制台模拟器 (console emulator)
##### [Clink](https://github.com/mridgers/clink)
Clink将GNU Readline 函数库整合进原生的Windows 命令行控制台，提供命令行模式下强大的编辑和输入能力
##### [msysgit](https://github.com/msysgit/msysgit)
提供了Git for Windows 相关工具，和Unix/Linux 环境下常用的命令行工具，如 less, ls, tar, unzip, md5sum, grep, sed ... 等


### 安装
安裝 cmder

先到 cmder 官网 http://cmder.net/ 下载此工具，有两个版本，分別是 mini 和 full 版，区别在于是否内建msysgit工具集。在电脑未安装Git for Windows等工具集的时候，建议可以下载full版使用。

### 外部链接
具体使用就不想介绍了，网上有详细的使用介绍和说明，这里推荐3篇文章，大家可以看一下：

* 介紹好用工具：Cmder ( 具有 Linux 溫度的 Windows 命令提示字元工具 )
https://blog.miniasp.com/post/2015/09/27/Useful-tool-Cmder.aspx


* Win下必备神器之Cmder
https://jeffjade.com/2016/01/13/2016-01-13-windows-software-cmder/

* cmder 一个比cmd强n倍的神器
https://www.jianshu.com/p/7a706c0a3411


### 配置

#### 注册Cmder，文档的右键菜单添加Cmder here

```bash
Cmder.exe /REGISTER ALL
```

#### Cmder使用ls中文显示乱码解决方案
##### 方案1
默认配置不支持使用ls显示中文命名的文件列表。

解决方法：
按下Win+Alt+P打开设置。
在StartUp - Environment里添加

```bash
set LANG=zh_CN.UTF-8
set LANG=zh_CN.GBK
set LC_ALL=zh_CN.utf8
```
保存配置

重启Cmder，发现使用ls，中文正确显示了。

##### 备注
因ConEmu的bug，该设置无法保存，可以去
https://github.com/Maximus5/ConEmu/releases
下载最新或历史版本的ConEmu，替换Cmder自带的版本，则可以解决这个问题

##### 方案2
修改cmder\vendor\conemu-maximus5\ConEmu.xml文件

```bash
<value name="EnvironmentSet" type="multi">
	<line data="set PATH=%ConEmuBaseDir%\Scripts;%PATH%"/>
</value>
```

修改添加为：

```bash
<value name="EnvironmentSet" type="multi">
	<line data="set PATH=%ConEmuBaseDir%\Scripts;%PATH%"/>
	<line data="set LANG=zh_CN.UTF-8"/>
	<line data="set LANG=zh_CN.gbk"/>
	<line data="set LC_ALL=zh_CN.utf8"/>
</value>
```

### 结

就这样，配置完成后，就可以用啦~