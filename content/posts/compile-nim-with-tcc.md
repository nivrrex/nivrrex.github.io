+++
date = "2019-11-17T9:09:00+08:00"
draft = false
title = "compile nim with tcc"
tags = ["nim","tcc"]

+++

### 缘来
Nim语言可以转换成C、C++、Objective-C、JavaScript等语言，其中C语言默认是转换成GCC进行编译的，相较于Go语言来说，编译速度还是有点慢的。于是兴起了用Tcc编译器编译Nim语言的想法。

<!-- more -->
---
### 配置
其实配置很简单，打开nim\config\nim.cfg文件

```bash
cc = gcc
修改为
cc = tcc
```

### 结

就这样，保存文件配置完成后，就可以用Tcc编译Nim语言啦~