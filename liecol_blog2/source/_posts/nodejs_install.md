---
title: nodejs安装及环境搭建
date: 2015-09-30 17:54:05
tags: nodejs
categories:
- nodejs
---

nodejs安装(windows版本)：
下载地址：
(1) https://nodejs.org/en/
一路install
安装完了进cmd里的path查看一下
然后用node -version 测试一下
(2)安装环境
```javascript
npm express
```
express命令安装在全局才起作用
所以：
```javascript
npm install express -g
```
<!--more-->
新版本安装express后，还是会提示不是内部命令无法创建环境
npm install -g express@3.5.0 后面@加的是版本
新版本的解决办法是：
下载一个安装命令工具
```javascript
npm install -g express-generator
express -V 查看版本
```
(3)搭建自己的测试环境
```javascript
express mytest
```
(4)cd mytest
进入自己的测试环境
(5)npm install
添加所需依赖
(6)启动
```javascript
npm start
输入http://localhost:3000/
只要是3000端口都行，默认的。
```
接下来看看express的目录文件和文件夹
 
    app.js： 项目入口,相当于php项目中的 index.php、index.html
    node_modules： 存放项目的依赖库,比如所依赖的express，jade，debug等
    package.json： 项目依赖配置及开发者信息，文件默认内容如下：
        {
        "name": "mytest",
        "version": "0.0.0",
        "private": true,
        "scripts": {
            "start": "node ./bin/www"
        },
        "dependencies": {
            "body-parser": "~1.13.2",
            "cookie-parser": "~1.3.5",
            "debug": "~2.2.0",
            "express": "~4.13.1",
            "jade": "~1.11.0",
            "morgan": "~1.6.1",
            "serve-favicon": "~2.3.0"
        }
        }
    public： 静态文件如 css,js,img
    routes： 路由文件
    Views： 页面文件(Ejs或者jade的模板，默认是jade)
by liecol-晓斌
2015.09.30