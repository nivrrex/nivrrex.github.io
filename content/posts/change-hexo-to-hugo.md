+++
date = "2017-04-23T11:22:28+08:00"
draft = false
title = "change hexo to hugo"
tags = ["live","hexo","hugo"]

+++

#### 缘由
好久没有更新blog了，最近在重装系统并转移硬盘数据时，需要移动并删除hexo文件夹下的数据，结果提示“该文件夹包含名称过长且无法放入回收站的项目”，感觉好不爽。
而且重新布置hexo比较麻烦，要装一大堆插件，原先版本插件已经失效了。

于是就随便在网上搜索了一下，看看有什么替代的静态站点生成器，结果发现了：

**[hugo](https://gohugo.io/)** 这个go语言写的大杀器。

* A fast and modern static website engine
* Language:Go
* License:APL 2.0

目前在[StaticGen](https://www.staticgen.com/)上排名已经超过[Hexo](hexo.io)了，仅次于[Jekyll](jekyllrb.com)，于是兴起了试用的念头。

#### 迁移
因为blog上文章不多，迁移比较方便，主要是将Hexo的YAML转换为Hugo的TOML，网上有篇文章[从 Hexo 迁移到 Hugo](https://github.com/nodejh/nodejh.github.io/issues/11)介绍了如何迁移

#### 下载hugo
hugo就是一个执行文件，可以直接从[官网](https://github.com/spf13/hugo/releases)下载

windows下执行环境可以参考[https://gohugo.io/tutorials/installing-on-windows/](https://gohugo.io/tutorials/installing-on-windows/)

#### 生成站点
可以到[Hugo - Introduction to Hugo](http://gohugo.io/overview/introduction/)自行学习

**[Hugo - Hugo Quickstart Guide](http://gohugo.io/overview/quickstart/)**  快速上手指南

生成站点比较简单：

```bash
hugo new site sitename
```

#### 主题
可以到以下站址找自己喜欢的主题，我选择的是 ~~[hugo_eiio](https://themes.gohugo.io/hugo_eiio/)~~ [hermit](https://github.com/Track3/hermit)主题

* [Hugo Themes Site](https://themes.gohugo.io/)
* [hugoThemes: All Themes Hugo](https://github.com/spf13/hugoThemes)

安装比较简单：

```bash
cd themes
git clone https://github.com/Track3/hermit.git themes/hermit
```
#### 生成页面

```bash
hugo new posts/about.md
```
然后用自己喜欢的MD编辑器编辑就行啦，我用的是这款 [MarkdownEditor](https://github.com/jijinggang/MarkdownEditor)

#### 本地测试

```bash
hugo server --buildDrafts --watch --theme themename
```
运行后可以在 [http://localhost:1313/](http://localhost:1313/) 查看具体效果

#### 生成静态页面
还是一个命令：

```bash
hugo
```
基本上页面数量少的话，都是在ms级别，生成文件在public文件夹内

#### 发布到github
将public文件夹中文件和github.io进行同步即可

---
#### 结
至此，新的blog已经搭建完成了，虽然存在现在theme没有之前用hexo的theme好看，但是胜在简单，而且后续还可以自己慢慢调整，整体还是比较喜欢的 ^o^ ~
