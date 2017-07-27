---
title: python性能测量工具cProfile
date: 2017-07-27 15:17:40
tags: [python,cProfile]
categories:
- python
---

## 背景：
Python是一种解释性的语言，执行速度相比C、C++等语言十分缓慢；因此我们需要在其它地方上下功夫来提高代码的执行速度。
首先需要对代码进行分析，这个时候则需要用一些工具。
这里介绍cProfile：
全代码分析：
命令行: cProfile -s tottime your_program.py
结果如下：

```Python
ncalls  tottime  percall  cumtime  percall filename:lineno(function)
   66    0.001    0.000   11.850    0.180  base.py:228(micro_service)
   66    0.003    0.000   11.849    0.180  tools.py:557(micro_service)
 1056    0.001    0.000   11.073    0.010  connection.py:463(drain_events)
 1056    0.015    0.000   11.072    0.010  connection.py:466(blocking_read)
 1056    0.008    0.000   10.920    0.010  transport.py:233(read_frame)
 3168    0.014    0.000   10.908    0.003  transport.py:370(_read)
 3168   10.892    0.003   10.892    0.003  {method 'recv' of '_socket.socket' objects}
   66    0.001    0.000    9.814    0.149  rpc.py:350(__call__)
   66    0.001    0.000    8.395    0.127  rpc.py:329(result)
```
<!--more-->
## 块分析:
上面属于文件分析,但是我们可能只对部分代码感兴趣，那么只需要在这部分代码的前后加上下面这两段代码即可：
```Python
import cProfile
cp = cProfile.Profile()
cp.enable()

YOUR CODE

cp.disable()
cp.print_stats()
```
结果与全代码分析的类似，但是只包含你感兴趣的部分。

## 行分析:
行分析需要安装line_profiler：
```Python
pip install line_profiler
```
```Python
@profile
def class_name()
	pass
```
然后在命令行输入：
```Python
kernprof -l -v your_code.py
-l 逐行分析
-v 立即查看结果
```

## 示例：
```Python
from cProfile import Profile as profile 
from pstats import Stats 
def profile_module(): 
	p = profile() 
	p.snapshot_stats() 
	p.enable() 
	# 需要调优的代码
	p.disable() 
	p.print_stats(2) # 按照调用累加总耗时累加排序，即将最耗时的函数最优先
	p.dump_stats("call.log")
```
关于profile和cProfile的更多链接，请点击：
https://docs.python.org/3/library/profile.html?spm=5176.100239.0.0.qa5fU5