+++
date = "2024-05-10T10:00:00+08:00"
draft = false
title = "use mupdf to read epub and use css set epub font"
tags = ["soft","epub"]

+++

### 缘来

最近用手机太多，眼睛不舒服，于是拿起前期买的墨水屏阅读器，准备用起来，先是来看下小说，看看习惯后再过度到其它书籍，期间使用情况记录一下。

<!-- more -->

---

### 获取电子书

目前电子书主要是 txt 、mobi 、epub 等格式的。txt没有格式，看起来不是那么舒服，所以下载后会转成其它格式使用。mobi 是私有格式，而亚马逊已经在2023年6月30日停止Kindle电子书店的运营了，在试用部分软件过程中，发现个别打开 mobi 的会出现卡顿，所以尽量不考虑，只有 mobi 格式的也尽量转换成其它格式使用。

最终考虑的是使用开源的 epub 格式书籍。

### 格式转换

目前功能最强的软件是 calibre ，可以组织、管理、阅读各类电子书籍，大而全，其它软件解决不了问题时考虑使用。

##### kaf-cli

从网上找到了一个软件 kaf-cli，可以把 txt 文本转成 epub 和 mobi 电子书的命令行工具。[GitHub - ystyle/kaf-cli](https://github.com/ystyle/kaf-cli) ，自动识别章节、生成目录，还有很多细节的设置，比较方便。

如果只是在墨水屏阅读器上使用，则转换生成的电子书直接导入阅读器即可。

### linux 下 EPUB 阅读软件 - mupdf

近期都是使用 linux 系统，windows 已经沦为备用，所以还是要找到合适的 linux 下 epub 阅读软件。但是找了很多，都没有符合我心意的，要么大而全，要么使用不方便，而且很多都是 js 或者 python 写的，性能和占用不太爽。

最终找到了 mupdf ，是一个用 C 语言写的开源阅读器，可以支持 PDF 、XPS、EPUB 格式阅读，速度很快，占用也低，缺点就是设置需要命令行或者快捷键，使用入门门槛高。

##### mupdf 命令行参数

```bash
usage: mupdf [options] file.pdf [page]
        -p -    password
        -r -    resolution
        -A -    set anti-aliasing quality in bits (0=off, 8=best)
        -C -    RRGGBB (tint color in hexadecimal syntax)
        -W -    page width for EPUB layout
        -H -    page height for EPUB layout
        -I -    invert colors
        -S -    font size for EPUB layout
        -U -    user style sheet for EPUB layout
        -X      disable document styles for EPUB layout
```

##### mupdf 快捷键

| 快捷键               | 功能           |
|:-----------------:|:------------:|
| f                 | 切换全屏         |
| w                 | 调整窗口来适配阅读页面  |
| W                 | 调整页面宽度适配窗口   |
| A                 | 开启或关闭抗锯齿     |
| [number]z         | 按照DPI设置缩放比例  |
| +                 | 放大           |
| -                 | 缩小           |
| [                 | 逆时针旋转        |
| ]                 | 顺时针旋转        |
| 空格、PageDown、↓、>、j | 向后翻页         |
| b、PageUp、↑、<、k    | 向前翻页         |
| [number]g         | 跳转至[number]页 |
| /                 | 向前搜索         |
| ?                 | 先后搜索         |
| n                 | 继续向前搜索       |
| N                 | 继续向后搜索       |

基本上以上的快捷键，足够日常使用了。

##### css 设置 EPUB 字体

mupdf 设置字体稍微麻烦点，也是要通过命令行参数解决。通过 "-U" 参数指定外部 css 文件进行设置。

```css
@font-face {
    font-family: 'HYQiHei 55S';
    font-weight: normal;
    font-style: normal;
    src:url(/etc/mupdf/HYQiHei.ttf) format('turetype');
}

html, body {
    font-family: "HYQiHei 55S";
    font-size: 16pt;
    /* 文字:墨绿色 #414c41 rgb(65,76,65) 背景色:箬竹色 #b5eecd rgb(181,238,205) */
    /* 文字:墨绿色 #494c49 rgb(73,76,73) 背景色:赭石色 #d5c6ac rgb(213,198,172) */
    color: rgb(65,76,65);
    background-color: rgb(181,238,205);
}
```

我这里使用的是汉仪旗黑字体，放置在了 /etc/mupdf/ 目录下，字体大小、颜色、背景色也有所调整。

### 结

至此，从 txt 文件转成 epub ，用 mupdf 打开 epub 文件，并通过 css 设置 epub 文件的字体和背景等都已解决，可以开心的在电脑和墨水屏阅读器上看书啦~
