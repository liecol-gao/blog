---
title: 关于Hexo搭建Blog托管至Coding并显示
date: 2016-11-14 17:08:37
tags: [hexo,coding,Blog]
categories:
- hexo
---

用hexo搭建了一个Blog，之前托管到github上，
但是因为github把百度的爬虫给禁掉了，又想让百度搜索能搜索到Blog,所以找了国内代码托管网站coding。
研究了一下hexo对于一些代码托管网站的支持。
这边记录一下coding。
coding因为改版的原因和以前的不一样，所以地址和对应的分支也不一样。

命名一个项目管理名，可以为自己的名字。
所在分支为: coding-pages(page服务可以开启也可以关闭)

通过与github同样的方式托管代码，这边不做太多解释(hexo下面贴代码)。
```
deploy:
   type: git
   repo:
      github: https://github.com/liecol-gao/liecol-gao.github.io.git,master
      coding: https://git.coding.net/liecol/liecol.git,coding-pages

```
关于域名定向：coding里，将自己的域名解析到pages.coding.me，然后这边再填入对应域名即可访问。

Create By
liecol-晓斌 
2016/11/14