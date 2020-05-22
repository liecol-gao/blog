---
title: Requests 一款实用的python http库
date: 2017-12-13 18:08:15
tags: [python,http]
categories:
- python
---

Requests 作为Python的一款实用型http库，一直深受好评。让人用完就不太想使用其他的http库。
对于http不太了解的开发，采用其他库，会经常出现安全隐患，性能差，不经意造轮子，代码冗余等病状，
所以，在笔者看来Requests还是比较友好和谐的一款http库。
接下来，我们来看一下Requests和非Requests的对比。
##### 非Requests：
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import urllib2

gh_url = 'https://api.github.com'

req = urllib2.Request(gh_url)

password_manager = urllib2.HTTPPasswordMgrWithDefaultRealm()
password_manager.add_password(None, gh_url, 'user', 'pass')

auth_manager = urllib2.HTTPBasicAuthHandler(password_manager)
opener = urllib2.build_opener(auth_manager)

urllib2.install_opener(opener)

handler = urllib2.urlopen(req)

print handler.getcode()
print handler.headers.getheader('content-type')

# ------
# 200
# 'application/json'
```
##### Requests：
```python
r = requests.get('https://api.github.com/users', auth=('user', 'pass'))
print r.status_code
# ------
# 200
```
对比后是不是感觉Requests非常的简洁明了。
<!--more-->
Requests 完全满足目前的web需求,包括:
  * Keep-Alive & 连接池
  * 国际化域名和 URL
  * 带持久 Cookie 的会话
  * 浏览器式的 SSL 认证
  * 自动内容解码
  * 基本/摘要式的身份认证
  * 优雅的 key/value Cookie
  * 自动解压
  * Unicode 响应体
  * HTTP(S) 代理支持
  * 文件分块上传
  * 流下载
  * 连接超时
  * 分块请求
  * 支持 .netrc

在这里，笔者不做安装介绍。主要介绍一下如何快速上手。
##### 发送请求：
GET, POST , PUT, DELETE, HEAD, OPTIONS 发送方式一应俱全。
```python
r = requests.get('https://github.com/timeline.json')
r = requests.post("https://github.com/timeline.json")
r = requests.put("https://github.com/timeline.json")
r = requests.delete("https://github.com/timeline.json")
r = requests.head("https://github.com/timeline.json")
r = requests.options("https://github.com/timeline.json")
```
##### 传递URL参数
```python
payload = {'key1': 'value1', 'key2': 'value2'} #可以采用dict的方式。
r = requests.get("http://httpbin.org/get", params=payload)
```
解析出URL，我们就会发现URL已被正确编码：
```python
print r.url
http://httpbin.org/get?key2=value2&key1=value1
```
##### 响应内容
```python
import requests
r = requests.get('https://github.com/timeline.json')
print r.text
u'[{"repository":{"open_issues":0,"url":"https://github.com/...
```
Requests会自动解码来自服务器的内容,大多数unicode字符集都能被它无缝解码。
##### JSON 响应内容
Requests中也有一个内置的JSON解码器，可以帮助处理JSON数据：
```python
r = requests.get('https://github.com/timeline.json')
r.json() #如果JSON解码失败,r.json()就会抛出一个异常。
```
##### 定制请求头
和参数一样，可以采用dict的方式加入请求头数据。
```python
url = 'https://github.com/timeline.json'
headers = {'user-agent': 'my-app/0.0.1'}
r = requests.get(url, headers=headers)
```
注意：
  * 如果被重定向，定制的请求头就没啥用，会被删掉。
  * 如果采用代理，同上。
  * Content-Length 会存在被改写的情况。

##### 响应状态码
```python
r = requests.get('https://github.com/timeline.json')
r.status_code
200
```
##### 响应头
```python
r.headers
{
    'content-encoding': 'gzip',
    'transfer-encoding': 'chunked',
    'connection': 'close',
    'server': 'nginx/1.0.4',
    'x-runtime': '148ms',
    'etag': '"e1ca502697e5c9317743dc078f67693f"',
    'content-type': 'application/json'
}
```
##### 获取Cookie
```python
url = 'https://github.com/timeline.json'
r = requests.get(url)
r.cookies['example_cookie_name']
'example_cookie_value'
```
##### 发送Cookie
```python
url = 'https://github.com/timeline.json'
cookies = dict(cookies_are='working')
r = requests.get(url, cookies=cookies)
r.text
```

##### 超时
我们可以告诉requests在经过以timeout参数设定的秒数时间之后停止等待响应。
timeout 仅对连接过程有效，与响应体的下载无关。
```python
requests.get('http://github.com', timeout=0.001)
```