---
title: Date.parse在IE里NaN问题的解决方式
date: 2017-04-23 13:59:50
tags: javascript
categories:
- javascript
---

之前处理时间问题发现ie浏览器里，Date.parse不起效果，兼容解决方式如下：
```javascript
var start_time = test_time.replace(/-/g,'/');
start_time = new Date(start_time);
var result = start_time.getTime();
```
create By liecol-晓斌

2017.04.23