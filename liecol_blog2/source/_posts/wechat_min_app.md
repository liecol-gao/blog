---
title: 关于开发wechat小程序
date: 2017-01-09 23:54:50
tags: wechat
categories:
- wechat
---

小程序今日上线致敬乔布斯，朋友圈一堆言论刷屏以及一片卧槽声起伏不定。
博主也不能免俗的研究了一番，注册了一个小程序，写了个demo。
首先发表点个人对该产品的观点：
* 用完即走，目前看来第一批试用者倒是实实在在的没有玩坏，不过根据文档来看，玩坏是早晚的事儿。
* 与其他应用共用UnionID，这种事儿就能看出腾讯心很大。
* 优势在于开发上速度会更快，用户量以及用户推广也会方便不少。
**说说技术坑点：**
* https,博主不想说脏话。
* rpx,博主再次不想说脏话。
* 自己杜撰程序脚本，博主还是不想说脏话。
<!--more-->

**说一下它的技术优势:**
* 微信api调用方便。
* h5处理方便。
* 少了公众号授权步骤，加载上有微提升。

接下来说说对开发文档的理解，个人觉得其有仿js框架的嫌疑，属于野路子里杜撰的一个脚本规则：
其实小程序的文档写的还是蛮清楚的：

* app.json 配置
* app.js 脚本
* app.wxss 样式
* test.wxml 类似html的标签脚本

*小程序页面由以上4部分组成，当然这4个需要放置在同一个目录以及配置同一个文件名称。*

------

* wx.api 微信原生API

app.json
结构与json一致：
```javascript
{"a":a, "b":b, "c":c}
```

从他们官方的demo里我摘出了一部分初始配置：

* pages指显示需要用到的一些page路径;
* window指对该窗口的一些配置;
* tabBar指的是tab菜单;
* networkTimeout指窗口超时配置。

```javascript
{
  "pages": [
    "page/component/index",
    "page/API/storage/storage"
  ],
  "window": {
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "演示",
    "navigationBarBackgroundColor": "#fbf9fe",
    "backgroundColor": "#fbf9fe"
  },
  "tabBar": {
    "color": "#dddddd",
    "selectedColor": "#3cc51f",
    "borderStyle": "black",
    "backgroundColor": "#ffffff",
    "list": [{
      "pagePath": "page/component/index",
      "iconPath": "image/icon_component.png",
      "selectedIconPath": "image/icon_component_HL.png",
      "text": "组件"
    }, {
      "pagePath": "page/API/index/index",
      "iconPath": "image/icon_API.png",
      "selectedIconPath": "image/icon_API_HL.png",
      "text": "接口"
    }]
  },
  "networkTimeout": {
    "request": 10000,
    "connectSocket": 10000,
    "uploadFile": 10000,
    "downloadFile": 10000
  },
  "debug": true
}
```
app.js
结构(obj参数)：
注册程序：
```javascript
App({
  onLaunch: function () {
    console.log('App Launch')
  },
  myfunction: function () {
    ...
  }
})
```

注册页面：
```javascript
Page({
  data: {
    texts: [
      '第1个',
      '第2个',
      '第3个'
    ]
  },
  tabchange: function(e){
    console.log('第 ' + e.detail.current + ' 个t')
  }
})
```

结构基本如上显示，后面可根据自己的需要来增加对应代码，当然在脚本app.js也依然可以申明js变量。

app.wxss 
```javascript
1rpx = 0.5px
```
这里面除了rpx比较搞，其他的都还好。

test.wxml
这里test是我自己写的。
类似于html的标签脚本
```html
<view/>、<image/>、<text/>
<input placeholder="liecol_input_自动对焦" auto-focus/>
```
这玩意儿刚开始不对应对应，还真有可能会乱掉。

标签数据绑定
这就是绝对的js框架搬过来了
```javascript
{{ message }}
Page({
  data: {
    message: 'Hello LIECOL!'
  }
})
```
这里的事件还是蛮有意思的，写起来也很轻松。
例如：

* longtap 手指触摸后，超过350ms再离开
* touchcancel 手指触摸动作被打断，如来电提醒，弹窗
等等...
这个官方文档写的很清楚，我在这里就不再赘述。

wx.api 
微信原生api.
如果大家都开发过公众号的话，相信很清楚，根据对应的微信原生api调用方式调用即可。
在小程序这边都是以wx.作为前缀。
这些原生的微信api接口还是非常实用的，文档也很清楚，在这仅举一例即可：
```javascript
App({
  onLaunch: function() {
    wx.login({
      success: function(res) {
        if (res.code) {
          //发起网络请求
          wx.request({
            url: 'https://test.com/onLogin',
            data: {
              code: res.code
            }
          })
        } else {
          console.log('获取用户登录态失败！' + res.errMsg)
        }
      }
    });
  }
})
```

* APP:界面程序里
* onLaunch为：生命周期函数--监听小程序初始化
* wx.login 为微信的原生api接口
* wx.request 为发起的 HTTPS 请求。
* url为地址
* data为参数

关于小程序基本的结构介绍差不多就是这些.
还有一些关于继承A-继承->B-继承->C 但是A-/继承->C
一类的就让大家在开发过程中自己去探索吧。
Create By
liecol-晓斌
2017-01-09












