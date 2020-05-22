---
title: 如何解决jquery的$符号冲突
date: 2016-07-14 17:48:37
tags:
---

在调用js的时候，如果其他JavaScript库为它的函数使用 $ 时，这就涉及到需要解决变量$的冲突
方法为:
(1)使用 noConflict() 方法为 jQuery 可以为初始变量$规定新的名称
```javascript
var jq=$.noConflict();
```
所以之前的$("#test")...样式将变成为
$("#test")... => jq("#test")...

(2)用完之后便可以还原jquery初始变量，removeAll的值为bool值，用作指示是否允许彻底将 jQuery 变量还原。
```
jQuery.noConflict(removeAll)
```
(3) $.noConflict() 如果出现冲突，jquery至于其后，采用$.noConflict()可以返回冲突js库的$控制权

(4)  .ready()给对象取别名，可以与3联合使用
如下:
```
<script type="text/javascript">
    $.noConflict();
    jQuery(document).ready(function($) {
        // 使用 jQuery $ 的代码
    });
        // 使用其他库的 $ 的代码
</script>
```
或者：
```
jQuery.noConflict()(function(){
    // 使用 jQuery 的代码
});
... // 其他库使用 $ 做别名的代码
```
还有一些其他方式，大同小异，不方便记忆，笔者就不一一列举了，希望对大家有帮助。
liecol-晓斌 by 2015/08/27