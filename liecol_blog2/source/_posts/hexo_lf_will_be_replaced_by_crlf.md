---
title: hexo LF will be replaced by CRLF 错误
date: 2017-01-10 16:32:52
tags: [hexo,Blog]
categories:
- hexo
---

今天用hexo deploy 到github和coding，发现如下错误。
```
warning: LF will be replaced by CRLF in tags/wechat/index.html.
The file will have its original line endings in your working directory.
```

看到网上资源很少，这边做个记录。
解决方式为：
* 删除.deploy_git 目录
* git config --global core.autocrlf false
* hexo clean
* hexo g
* hexo d














