---
title: php常用函数总结
date: 2016-11-19 12:22:00
tags: [php]
categories:
- php
---

数据类型转换：
第一种转换方式： (int)  (bool)  (float)  (string)  (array) (object)
eg:
`(int)$num1`
第二种转换方式：  intval()  floatval()  strval()
eg:
`intval($str)`
第三种转换方式：  settype();//强制转换类型
`settype($num,"int");`
<!--more-->
```php
unset(var) //删除指定变量
sleep() //函数延迟代码执行若干秒。
eval() //函数把字符串按照 PHP 代码来计算。
defined() //函数检查某常量是否存在。
define() //函数定义一个常量。
trim() //函数从字符串的两端删除空白字符和其他预定义字符。
substr_replace() //函数把字符串的一部分替换为另一个字符串。
substr_count() //函数计算子串在字符串中出现的次数。
strrev() //函数反转字符串。
strlen() //函数返回字符串的长度。
stristr() //函数查找字符串在另一个字符串中第一次出现的位置。
stripos() //函数返回字符串在另一个字符串中第一次出现的位置。
str_split() //函数把字符串分割到数组中。
str_replace() //函数使用一个字符串替换字符串中的另一些字符。
md5() //函数计算字符串的 MD5 散列。
ltrim() //函数从字符串左侧删除空格或其他预定义字符。
join(",",$arr) //函数把数组元素组合为一个字符串。
htmlspecialchars(str) //函数把一些预定义的字符转换为 HTML 实体。
htmlspecialchars_decode(string,flags)
htmlentities(str) //函数把字符转换为 HTML 实体。
html_entity_decode(str) //函数把 HTML 实体转换为字符。
explode(array) //函数把字符串分割为数组。
addslashes(string) //函数在指定的预定义字符前添加反斜杠。
stripslashes(string) //addslashes反函数。
round() //函数对浮点数进行四舍五入。
rand() //函数返回随机整数。
min() //返回最小值。
max() //返回最大值。
ceil() //函数向上舍入为最接近的整数。

array_change_key_case($input_array, CASE_UPPER); //CASE_LOWER 返回字符串键名全为大写或者小写的数组
array_chunk(array, int, true/false) //将一个数组分割成多个 （true/false是否保持键名）
array_combine(array, array)// 创建一个数组，用一个数组的值作为其键名，另一个数组的值作为其值
array_filter($array1, "filter") //用回调函数过滤数组中的单元
array_intersect(array,array) //返回两个数组的交集，键值不变
array_key_exists (key, array)//检查给定的键名或索引是否存在于数组中
array_keys(array)//返回数组的所有键值
array_map("cube", array)//返回一个数组，该数组包含了 arr1 中的所有单元经过 callback 作用过之后的单元
array_merge_recursive(array,array) //将两个或多个数组的单元合并起来,一个数组中的值附加在前一个数组的后面,返回作为结果的数组。
array_merge() //将两个或多个数组的单元合并起来，一个数组中的值附加在前一个数组的后面。返回作为结果的数组。
array_pad(array,-7,1)//用值将数组填补到指定长度(数组，位置，值)
array_pop (array) //弹出数组最后一个单元
array_push(array, str) //往数组追加内容
array_reverse(array,true) //翻转数组，如果 preserve_keys 为 TRUE 则保留原来的键名。
array_search(str,array) //在数组中搜索给定的值，如果成功则返回相应的键名
array_shift(array) //去除数组第一个元素
array_slice(array, 2,-1) //返回根据 offset 和 length 参数所指定的 array 数组中的一段序列.类似python的list切片。
array_sum(array) //将数组中的所有值的和以整数或浮点数的结果返回
array_unshift(array,str) //将传入的单元插入到 array 数组的开头
array_values(array) //返回数组中所有的值
arsort(array, sort_flags) //对数组进行逆向排序并保持索引关系
asort(array, sort_flags) //对数组进行顺向排序并保持索引关系
sort_flags:
SORT_REGULAR - 正常比较单元
SORT_NUMERIC - 单元被作为数字来比较
SORT_STRING - 单元被作为字符串来比较
count(array) //统计变量中的单元数目
current(array) //返回数组中的当前单元
end (array) //将 array 的内部指针移动到最后一个单元，并返回该单元的值。
in_array(str,array) //检查数组中是否存在某个值
krsort() //对数组按照键名逆向排序，保留键名到数据的关联
ksort() //对数组按照键名顺序排序，保留键名到数据的关联。本函数主要用于结合数组
rsort() //本函数对数组进行逆向排序（最高到最低）。
uksort(array, 'str') //键名进行排序, 采用用户自定义函数
uasort(array, 'str') //键值进行排序，采用用户自定义函数
usort(array, 'str') //使用用户自定义的比较函数对数组中的值进行排序
```
create By 
liecol-晓斌 
2016-11-19

