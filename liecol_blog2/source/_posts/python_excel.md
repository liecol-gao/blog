---
title: python导入excel表数据
date: 2016-07-07 16:58:00
tags: [python,xlrd,excel]
categories:
- python
---

1:安装第三方包
```python
import xlrd
```
2:读取文件
```python
file_content = xlrd.open_workbook('excelFile.xls')
```
这边看了一下源码，它和csv不一样，是在内部打开文件，所以传输路径就可以，如果想传内容，可以稍微改一下。
<!--more-->
3:获取工作表
```python
1)table = file_content.sheets()[0]//索引
2)table = file_content.sheet_by_index(0)//索引 
3)table = data.sheet_by_name(u'Sheet1')//通过名称
```
4:获取行数和列数
```python
nrows = table.nrows //获取行数
ncols = table.ncols 获取列数
```
5:获取数据
```python
table.row_values(数字)
table.col_values(数字)
```

6:循环获取数据
```python
for i in range(nrows):
    print table.row_values(i)
```
7:获取某单元格
```python
cell_A1 = table.cell(0,0).value  //列行，索引从0开始
cell_C4 = table.cell(2,3).value  //列行，索引从0开始
```
只是简单的读取了数据，其他的没做练习，在这就不再copy网上的了。有需要的，大家自己去研究， 比php的简单多了。
注：后来因为业务，导致长度不够，后改为openpyxl使用了。
create By
liecol-晓斌
2016/07/07