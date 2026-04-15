+++
date = "2014-06-15T17:24:30+08:00"
draft = false
title = "some problems for golang excel update"
tags = ["golang"]

+++

趁着周末，用golang把以前写的excel操作的简单包装库又修改了一下，具体详见[这里](https://github.com/nivrrex/excel)
由于对golang的语法和工具链不怎么熟悉，反复了好几次，下边简单记录下。
<!-- more -->

---
#### 首字母大小写导致无法外部调用
Golang的package中的函数或者，首字母大写是Public，外部可见的。首字母小写是Private，仅限内部访问。写的package里的struct的内部属性就是因为这个原因，无法被外部调用。这是Golang为了简化关键字，做的一项规定。

---
#### GOPATH设置
golang有几个关键环境变量，需要进行设置

* GOROOT : Golang的安装目录，默认为C:\Go
* GOPATH : 用来指定在$GOROOT之外的各种外部package，包含src和pkg文件的源目录
* GOBIN : Golang安装目录下的bin目录，默认为C:\Go\bin
* GOOS : 目标平台（编译后的目标平台）的操作系统（darwin、freebsd、linux、windows）
* GOARCH : 目标平台（编译后的目标平台）的处理器架构（386、amd64、arm）

我就是没有好好设置GOPATH环境变量，结果默认为GOROOT，下载的package，在go build和go install后，无法被找到，还需要手动复制到GOROOT对应的目录下，结果我认为是Golang工具链的BUG －_－^

``` Batch
mkdir C:\Go\import
mkdir C:\Go\import\src
mkdir C:\Go\import\pkg
set GOPATH=C:\go\import
```

---
#### Go build无法生成文件
这个属于我对Go工具链的不了解，Go build就是无法生成.a文件的，生成.a文件的是Go install－_－^

---
#### 小结
归根结底，还都是自己对Golang相关语法及工具链不熟悉的问题。
Golang是个好东西，就是太年轻，好多东西需要自己重新造轮子，而且它有着自己的适用范围。
日常的自动化工作等问题的解决，还是用Ruby等成熟的脚本语言比较效率。
