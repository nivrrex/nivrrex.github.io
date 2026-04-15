+++
date = "2014-06-12T19:37:32+08:00"
draft = false
title = "Hello GitHub"
tags = ["live","hexo"]

+++


欢迎来到我的[GitHub blog](http://nivrrex.github.io/)！

这是我用[Hexo](http://hexo.io/)编写的一个简易blog，将记录我在GitHub和生活中的点点滴滴，主要关注Ruby和Golang。

---

#### Git的一些基本命令
<!-- more -->
```javascript
git config --global user.name "nivrrex"
git config --global user.email nivrrex@gmail.com

ssh-keygen -t rsa -C "nivrrex@gmail.com"
ssh -T git@github.com -i ./.ssh/id_rsa
```

更多命令可以到[git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)去查看

---

#### Hexo的一些基本命令
```javascript
npm install hexo-cli -g

npm install hexo --save
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-generator-sitemap --save
npm install hexo-generator-feed --save
npm install hexo-renderer-ejs --save
npm install hexo-renderer-stylus --save
npm install hexo-renderer-marked --save
npm install hexo-server --save
npm install hexo-deployer-git --save

hexo init
hexo new [post] "My New Post"
hexo server
hexo generate
hexo deploy
```

更多文档可以到[Hexo docs](http://hexo.io/docs/)上查看

---
#### Markdown语法
可以去[不如](http://ibruce.info/)的[Markdown简明语法](http://ibruce.info/2013/11/26/markdown/)去学习下
