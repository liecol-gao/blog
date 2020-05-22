---
title: hexo 添加百度谷歌收录sitemap
date: 2017-01-10 16:51:52
tags: [hexo,Blog]
categories:
- hexo
---
**自动推送:**
最近发现百度没收录blog子页，然后就研究了一番。
之前在theme里添加了一段百度自动推送的js：

```javascript
<script>
(function(){
    var bp = document.createElement('script');
    var curProtocol = window.location.protocol.split(':')[0];
    if (curProtocol === 'https') {
        bp.src = 'https://zz.bdstatic.com/linksubmit/push.js';        
    }
    else {
        bp.src = 'http://push.zhanzhang.baidu.com/push.js';
    }
    var s = document.getElementsByTagName("script")[0];
    s.parentNode.insertBefore(bp, s);
})();
</script>
```
<!--more-->
**sitemap提交:**
感觉没啥人点，自己也懒得点，算了，再补一个sitemap。
hexo 站点地图的插件安装方式如下：
google：
```javascript
npm install hexo-generator-sitemap --save
```
百度：
```javascript
npm install hexo-generator-baidu-sitemap --save
```
之后编译Blog
```JavaScript
hexo g
```

http://bin.bloggao.cn/sitemap.xml
http://bin.bloggao.cn/baidusitemap.xml

**主动推送(这个效率最高):**
```nodejs
npm install hexo-baidu-url-submit --save
```
根目录_config.yml配置文件：
```
baidu_url_submit:
  count: 3 ## 提交最新的链接数量
  host: bin.bloggao.cn ## 域名
  token: your_token ## 百度主动推送的token，私钥
  path: baidu_urls.txt ## 主动推送的文件地址
```

追加deploy
```
deploy:
- type: baidu_url_submitter
```
比如博主的是同时到coding和github的：
```
deploy:
- type: git
  repo: 
    github: https://github.com/liecol-gao/liecol-gao.github.io.git,master
    coding: https://git.coding.net/liecol/liecol.git,coding-pages
- type: baidu_url_submitter
```

