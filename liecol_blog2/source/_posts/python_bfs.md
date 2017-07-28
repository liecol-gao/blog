---
title: 图的基本算法bfs浅析
date: 2017-07-28 17:10:45
tags: [python,bfs]
categories:
- python
---


关于图的搜索有两种：广度优先（bfs）深度优先 （dfs）。

|   |   |   | A |   |   |   |
|---|---|---|---|---|---|---|
|   | B |   |   |   | C |   |
| D | E | F |   | G |   | H |
|   |   |   |   | I |   |   |

dfs:顺序应该为：ABCEDF CGH I

bfs:顺序应该为：ABC EDF GH I

笔者总结：深度优先的过程也就是递归的过程, 而bfs是层级的过程，所以广度优先不可避免的需要用到队列。

<!--more-->
以下是结合需求用Python写的bfs代码(为了解决一个涉行的最短选择)，因涉及需求，笔者有注释，仅供参考。

```python
def get_rows(s_row, product_list, slot_p_count):
    # product_list eg:['p2', 'p3', 'p1', 'p1']
    # slot_p_count eg: {'02': 1, '03': 2, '08': 3}
    end_rows_path = []
    for num in range(0, len(s_row)): 
    	status = 0
        row_order = []
        temporary_order = []
        copy_p_list = copy.deepcopy(product_list)
        for row in s_row:
            if num > status:
            	status += 1
                continue
        	# get list tree intersection (must for del)
            pro_in_slot = False
            for pro in s_row[row]:
                try:
                    pro_ad = copy_p_list.index(pro)
                    del copy_p_list[pro_ad]
                    pro_in_slot = True
                except Exception, e:
                    pass
            if pro_in_slot:
            	row_order.append([row, slot_p_count[row]])
            if not copy_p_list:
            	temporary_order = copy.deepcopy(row_order)
                break
        if temporary_order:
            end_rows_path.append(temporary_order)
    # all end_rows_path eg: [['01', 5], ['05', 1], ...]  [row, all pro quantity]  Reason: less quantity priority
    return end_rows_path
```





