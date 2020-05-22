---
title: windows＋Python＋virtualenv环境配置
date: 2016-07-14 17:44:05
tags:
---

python安装

建议官网下载，因为可能会缺少pip和easy_install，没有这两个，需要耗时安装二者之一(由于2.7版本和3.X版本有一些区别，3.X有些库不支持,这里配置的是2.7.10)

python安装按提示进行，然后更改环境变量path即可

然后运行cmd输入python即能测试是否成功

官网文件会自动安装pip和easy_install，没有则需要手动安装其中之一，因为安装它们可以更加便捷的去安装更多的python第三方库。

这里也需要将script文件目录增至path环境变量

安装 virtualenv(虚拟环境)

为什么安装虚拟环境，1，避免版本冲突，2方便管理

安装pip之后安装virtualenv就很方便了

```
pip install virtualenv
```

由于python兼容性主要作用于linux上，切安装包以外网居多，有钱的老板可自行够买vpn，笔者耗了半天，连cmd都超时了，最后还是买了个vpn，感受到了人民币和非人民币玩家的区别。
```
You are using pip version 7.0.1, however version 7.1.0 is available.

You should consider upgrading via the 'pip install --upgrade pip' command.

Collecting virtualenv

  Downloading virtualenv-13.1.0-py2.py3-none-any.whl (1.7MB)

    100% |████████████████████████████████| 1.7MB 100kB/s

Installing collected packages: virtualenv

Successfully installed virtualenv-13.1.0

```
如上为安装完成

``
virtualenv test

virtualenv C:\test（如果要分离主环境，前面加路径即可）

New python executable in test\Scripts\python.exe

Installing setuptools, pip, wheel...done.
```
test即创建的工程文件，这个文件夹就是保存 Python 虚拟环境。

开启虚拟环境
```
Scripts\activate
```
Scripts 文件内部，在命令行下直接执行文件 activate

2015/08/07  15:42    <DIR>          .

2015/08/07  15:42    <DIR>          ..

2015/08/07  15:42             2,305 activate

(省略...)

如下即进入虚拟环境文件
```
(test) C:\python27\Scripts\test\Scripts>
```
在这里面 可使用 python，pip等命令，均是 virtualenv 配置好的，自己也可以自行下载其他库。
```
deactivate 
```
退出虚拟环境

virtualenv 创建的虚拟环境与主机的 Python 环境无关，主机配置的库不能在 virtualenv 中使用。你需要在虚拟环境中利用 pip install 再次安装配置后才能使用。

关于pyramid 如若有时间下次也会写出一段记录。

最后，涉及到数据库等环境变量，需要增加 --system-site-packages 用来调用系统数据库用作虚拟环境内部调用。否则程序跑步起来。
