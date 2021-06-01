---
title: '[转]用Python解数独'
date: 2021-06-01 23:15:47
categories: 
    - 程序开发
    - Python
tags:
    - python
---
> 芬兰数学家因卡拉花费3个月时间设计出的世界上迄今难度最大的数独。数独是 9 横 9 竖共有 81 个格子，同时又分为 9 个九宫格。规则很简单：每个空格填入 1~9 任意一个数字，需要保证每个横排和竖排以及九宫格内无相同数字。

![](259976-9986d86a0b148cfa.jpg)

解数独是一个可有可无的爱好，知道这个益智游戏，但是不很上心。但是前两天，由于自己的学生装了一个 ubuntu 18.04 的系统，上面有一些数独游戏，偶然间，让我看见了，为了更好的显摆自己的 Python 知识，决定用 Python 写一个程序，所以就有了下面的文字。

### 1、将待解的数独转换成 Python 矩阵

```
m = [
    [6, 0, 0, 1, 0, 0, 7, 0, 8],
    [0, 0, 0, 8, 0, 0, 2, 0, 0],
    [2, 3, 8, 0, 5, 0, 1, 0, 0],
    [0, 0, 0, 0, 4, 0, 0, 9, 2],
    [0, 0, 4, 3, 0, 8, 6, 0, 0],
    [3, 7, 0, 0, 1, 0, 0, 0, 0],
    [0, 0, 3, 0, 7, 0, 5, 2, 6],
    [0, 0, 2, 0, 0, 4, 0, 0, 0],
    [9, 0, 7, 0, 0, 6, 0, 0, 4]
] 
```

就是这么简单，将待填写的空白格用 0 来代替。

### 2、寻找第一个空格位置

```
def start_pos(m:"数独矩阵"):
    """ 功能：返回第一个空白格的位置坐标"""
    for x in range(9):
        for y in range(9):
            if m[x][y] == 0:
                return x, y
    return False, False  # 若数独已完成，则返回 False, False 
```

找到 Python 矩阵中第一个是 0 的元素的位置坐标。

### 3、寻找下一个空格位置

```
def get_next(m:"数独矩阵", x:"空白格行数", y:"空白格列数"):
    """ 功能：获得下一个空白格在数独中的坐标。       
    """
    for next_y in range(y+1, 9):  # 下一个空白格和当前格在一行的情况
        if m[x][next_y] == 0:
            return x, next_y
    for next_x in range(x+1, 9):  # 下一个空白格和当前格不在一行的情况
        for next_y in range(0, 9):
            if m[next_x][next_y] == 0:
                return next_x, next_y
    return -1, -1               # 若不存在下一个空白格，则返回 -1，-1 
```

找到 Python 矩阵中下一个是 0 的元素的位置坐标。详细内容看注释。

### 4、寻找适合当前空格的数字的集合

```
def value(m:"数独矩阵", x:"空白格行数", y:"空白格列数"):
    """ 功能：返回符合"每个横排和竖排以及
              九宫格内无相同数字"这个条件的有效值。
    """ 
    i, j = x//3, y//3
    grid = [m[i*3+r][j*3+c] for r in range(3) for c in range(3)]
    v = set([x for x in range(1,10)]) - set(grid) - set(m[x]) - \
        set(list(zip(*m))[y])
    return list(v) 
```

每个空格可以填入 1~9 中的任意一个数字，但要符合规则：每个空格填入 1~9 任意一个数字，需要保证每个横排和竖排以及九宫格内无相同数字。下面的代码中的 `grid` 变量，保存的是当前位置所处的九宫格。`v` 变量是通过集合运算，将 1~9 这个数字集合中，与行的数字集合、列的数字集合以及九宫格的数字集合重叠的部分去除掉。剩余的部分就是符合条件的数字的集合。

### 5、使用递归尝试解数独（Sudoku）

```
def try_sudoku(m:"数独矩阵", x:"空白格行数", y:"空白格列数"):
    """ 功能：试着填写数独 """
    for v in value(m, x, y):
        m[x][y] = v
        next_x, next_y = get_next(m, x, y)
        if next_y == -1: # 如果无下一个空白格
            return True
        else:
            end = try_sudoku(m, next_x, next_y) # 递归
            if end:   # 数独解完之后，此处的 end 会是 True
                return True
            m[x][y] = 0 # 在递归的过程中，如果数独没有解开，
                        # 则回溯到上一个空白格 
```

详细内容看注释。

### 6、代码展示

```
 import random 
import sys  
sys.setrecursionlimit(100000) # 发现python默认的递归深度是很有限的
                              #（默认是1000），因此当递归深度超过999的
                              # 样子，就会引发这样的一个异常。


def get_next(m:"数独矩阵", x:"空白格行数", y:"空白格列数"):
    """ 功能：获得下一个空白格在数独中的坐标。       
    """
    for next_y in range(y+1, 9):  # 下一个空白格和当前格在一行的情况
        if m[x][next_y] == 0:
            return x, next_y
    for next_x in range(x+1, 9):  # 下一个空白格和当前格不在一行的情况
        for next_y in range(0, 9):
            if m[next_x][next_y] == 0:
                return next_x, next_y
    return -1, -1               # 若不存在下一个空白格，则返回 -1，-1
        
def value(m:"数独矩阵", x:"空白格行数", y:"空白格列数"):
    """ 功能：返回符合"每个横排和竖排以及
              九宫格内无相同数字"这个条件的有效值。
    """ 
    i, j = x//3, y//3
    grid = [m[i*3+r][j*3+c] for r in range(3) for c in range(3)]
    v = set([x for x in range(1,10)]) - set(grid) - set(m[x]) - \
        set(list(zip(*m))[y])    
    return list(v)

def start_pos(m:"数独矩阵"):
    """ 功能：返回第一个空白格的位置坐标"""
    for x in range(9):
        for y in range(9):
            if m[x][y] == 0:
                return x, y
    return False, False  # 若数独已完成，则返回 False, False

def try_sudoku(m:"数独矩阵", x:"空白格行数", y:"空白格列数"):
    """ 功能：试着填写数独 """
    for v in value(m, x, y):
        m[x][y] = v
        next_x, next_y = get_next(m, x, y)
        if next_y == -1: # 如果无下一个空白格
            return True
        else:
            end = try_sudoku(m, next_x, next_y) # 递归
            if end:
                return True
            m[x][y] = 0 # 在递归的过程中，如果数独没有解开，
                        # 则回溯到上一个空白格

def sudoku(m):        
    x, y = start_pos(m)
    try_sudoku(m, x, y)
    print(m)     
    
        

                    
if __name__ == "__main__":
    m = [
        [6, 0, 0, 1, 0, 0, 7, 0, 8],
        [0, 0, 0, 8, 0, 0, 2, 0, 0],
        [2, 3, 8, 0, 5, 0, 1, 0, 0],
        [0, 0, 0, 0, 4, 0, 0, 9, 2],
        [0, 0, 4, 3, 0, 8, 6, 0, 0],
        [3, 7, 0, 0, 1, 0, 0, 0, 0],
        [0, 0, 3, 0, 7, 0, 5, 2, 6],
        [0, 0, 2, 0, 0, 4, 0, 0, 0],
        [9, 0, 7, 0, 0, 6, 0, 0, 4]
    ]

    sudoku(m)
    
""" 数独结果如下：
[
    [6, 9, 5, 1, 2, 3, 7, 4, 8], 
    [7, 4, 1, 8, 6, 9, 2, 5, 3], 
    [2, 3, 8, 4, 5, 7, 1, 6, 9], 
    [8, 1, 6, 7, 4, 5, 3, 9, 2], 
    [5, 2, 4, 3, 9, 8, 6, 7, 1], 
    [3, 7, 9, 6, 1, 2, 4, 8, 5], 
    [4, 8, 3, 9, 7, 1, 5, 2, 6], 
    [1, 6, 2, 5, 8, 4, 9, 3, 7], 
    [9, 5, 7, 2, 3, 6, 8, 1, 4]
]
""" 
```

* * *

原文地址 [www.jianshu.com](https://www.jianshu.com/p/867fe9528af1)