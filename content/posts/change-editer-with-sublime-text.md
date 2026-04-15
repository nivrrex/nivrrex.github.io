+++
date = "2015-08-01T15:41:45+08:00"
draft = false
title = "change editer with sublime text"
tags = ["soft","sublime-text"]

+++

#### 来由
本来一直使用的编译器是[SciTE](http://www.scintilla.org/SciTE.html)，很轻量级，自己设置了配置文件后，除了没有自动完成的提示，用起来也还可以。
最近看了[Golang中国的教学视频](http://www.tudou.com/home/golang/item/)，国内Golang语言框架作者Unknwon使用Sublime Text进行开发Macaron时，感觉自动提示用起来挺舒服的。而且又看到了一篇[Sublime Text 全程指南](http://www.linuxeden.com/html/news/20150730/162107.html)，改变了我对很久以前试用Sublime Text的看法。网络上有对它的评价非常非常高，兴起了试试Sublime Text的想法。

<!-- more -->

#### Sublime Text安装
首先到Sublime Text的[官网](http://www.sublimetext.com/)下载最新的版本，我下载的是3.3083的portable version版
然后是最重要的事情了，安装[Package Control](https://packagecontrol.io/installation#st3),它的地位相当于Ruby的Gem管理器了，基本上所有的Sublime Text都可以用它来安装管理。
打开 Sublime Text 后，同时按Ctrl+`键，调出控制台，输入以下命令：

```python
import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
#### Package Control及插件安装
根据提示，重启Sublime Text即可完成安装。
在Sublime Text中按ctrl+shift+p快捷键，即可调出Package Control控制台，它可以对Packages完成以下操作：

```bash
install
remove
list
enable
disable
upgrade
```
根据我的需求，我安装了以下插件

```bash
GoSublime             #go语言提示插件
SideBarEnhancements   #侧边栏增强插件
All Autocomplete      #自动完成增强插件
ConvertToUTF8         #GBK等中文文件编码增强插件
Markdown Preview      #Markdown文件预览插件
Markdown Extended     #Markdown语法扩展插件，可以对代码区域进行语法高亮
Monokai Extended      #主题插件，但是可以对Markdown进行语法高亮
JSONLint              #JSON的智能提示插件，对出错的语法进行实时标注
JSFormat              #可以对javascript进行格式化输出，同样对JSON也可以格式化
Material Theme        #语法高亮插件，Material MonoKai
```
#### Sublime Text配置
Sublime Text3的配置，主要采用JSON格式，后缀名是sublime-settings
不知道是不是Bug，其主要配置需要手动创建，菜单无法选择（后来证实是汉化包的Bug），于是根据网上提示，新建
C:\Sublime Text\Data\Packages\User\Preferences.sublime-settings文件，输入以下内容：

```json
{
    "font_face": "Consolas",                            //字体名称
    "font_size": 12,                                    //字体大小

    "caret_style": "phase",                             //光标闪烁
    "wide_caret": true,                                 //光标变粗

    "draw_white_space": "all",                          //显示所有空白符号
    "highlight_line": true,                             //高亮当前行
    "highlight_modified_tabs": true,                    //编辑过的标签高亮显示

    "auto_complete_commit_on_tab": true,                //tab键自动完成
    "tab_size": 4,                                      //tab键长度
    "translate_tabs_to_spaces": true,                   //自动将tab转换成空格

    "spell_check": false,                               //语法检查
    "trim_trailing_white_space_on_save": true,          //删除行尾的空格
    "ensure_newline_at_eof_on_save": true,              //文件结束后保存加入新行

    "update_check": false,                              //禁止自动更新

    "color_scheme": "Packages/Monokai Extended/Monokai Extended Bright.tmTheme",     //主题包Monokai

    "ignored_packages":                                 //忽略的包
    [
        "Vintage"
    ]
}
```

#### GoSublime配置
因为我用Golang和Ruby比较多，在安装了GoSublime后，还需要配置下GoSublime，以便进行编译
路径如下："Preferences" - "Package Setting" - "GoSublime" - "Settings User"

```json
{
    "env": {
        "PATH":"C:\\go\\bin",
        "GOPATH":"C:\\go\\import",
        "GOROOT":"C:\\go",
        "GOBIN":"C:\\go\\bin",
        "GOOS":"windows"
    },
    "build_command": ["go","run","$_fn"],

    "fmt_enabled": true,
    "fmt_tab_indent": false,
    "fmt_tab_width": 4
}
```

#### TinyCC编译系统
因为我偶尔用TinyCC编译小的C语言程序，所以自定义了一个编译系统，具体代码如下：

```json
{
  "path": "C:\\ Sublime Text\\tcc\\",
  "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
  "working_dir": "${file_path}",
  "selector": "source.c",

  "variants": [
    {
      "name": "Run",
      "cmd": ["tcc", "-run", "${file}"],
      "working_dir": "${file_path}"
    }
  ]
}
```

#### F5快捷键
平时喜欢把F5设置成脚本Run的快捷键盘，具体设置路径如下：”Preferences” - “Key Bindings”，在右部的User文件C:\Sublime Text\Data\Packages\User\Default (Windows).sublime-keymap增加如下代码：

```json
    { "keys": ["f5"], "command": "build" },
```

#### 结
试用了几天后，Sublime Text是比Scite要强大的多，虽然Scite也可以进行很多的配置，关键是Package Control上现成的插件太多了，有人维护的话，真是太省心了呢，已经将以前针对Scite写的部分脚本转移到Sublime Text上了，开心的编程啦~ \^o^/
