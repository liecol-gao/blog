---
title: jQuery上传插件FileUpload浅析
date: 2016-11-23 20:22:00
tags: [javascript,jquery,fileupload]
categories:
- javascript
---
对比了一下fileupload与其他的一些多图上传插件的对比
发现1fileupload1不需要对浏览器插件有支持，而且是一个神似`input file`的一个插件，因为业务需要，于是做了一下简单研究。
jQuery `File Upload`支持多文件上传、取消、删除，上传前缩略图预览、列表显示图片大小，支持上传进度条显示。

需要的文件：
```javascript
jquey-1.8.3.min.js
jquery-ui-widget.js
jquery.iframe-transport.js
jquery.fileupload.js
```
如果跨站点的话，需要一个iframe的文件，也可支持bootstrap样式修改。

需要的表单样式：
```html
<input id="fileupload" type="file" data-url="action_url" name="file[]" multiple>
```
action可直接放到input上也可自行添加url。
<!--more-->
```javascript
$("#fileupload_input").fileupload({
    url:"upload",//文件上传地址，当然也可以直接写在input的data-url属性内
    autoUpload: true,//是否自动上传  
	dataType: 'json',  
    formData:{param1:"p1",param2:"p2"},//如果需要额外添加参数可以在这里添加
    done:function(e,data){
        //返回的数据在result.result中，假设我们服务器返回了一个json对象
        console.log(JSON.stringify(result.result));
    }
})
```
回调函数：
```javascript
add: function (e, data) {
            data.context = $('<p/>').text('Uploading...').appendTo(document.body);
            data.submit();
        }
```
进度条：
```html
<div id="progress">
    <div class="bar" style="width: 0%;"></div>
</div>
```

```javascript
 progressall: function (e, data) {
        var progress = parseInt(data.loaded / data.total * 100, 10);
        $('#progress .bar').css(
            'width',
            progress + '%'
        );
    }
```
获取回调数据:
```javascript
done:function(e,data){
    console.log(JSON.stringify(result.result));
}
```
create By 
liecol-晓斌 
2016-11-23


