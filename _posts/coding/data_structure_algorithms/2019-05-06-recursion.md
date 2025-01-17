---
layout: post
title: Recursion
subtitle: 递归
author: Bin Li
tags: [Data Structures]
image: 
comments: true
published: true
typora-root-url: ../../../../binlidaily.github.io
---

　　递归方法是分治策略这种解决问题的思想的一种具体实现手段，递归算法是一种直接或间接调用自身函数或者方法的算法。通俗来说，递归算法的实质是把问题分解成规模缩小的同类问题的子问题，然后递归调用方法来表示问题的解。它有如下特点：

1. 一个问题的解可以分解为几个子问题的解
2. 这个问题与分解之后的子问题，除了数据规模不同，求解思路完全一样
3. 存在递归终止条件，即必须有一个明确的递归结束条件，称为递归出口

　　经典递归问题有：
1. 数据求和
2. 汉诺塔问题
3. 爬台阶问题

模板：

```python
void f()
{
     if(符合边界条件)
    {
        # ///////
        return;
    }
    
     # 某种形式的调用
     f();
}
```

## 优缺点
优点：

1. 简洁
2. 在树的前序，中序，后序遍历算法中，递归的实现明显要比循环简单得多。

缺点：

1.递归由于是函数调用自身，而函数调用是有时间和空间的消耗的：每一次函数调用，都需要在内存栈中分配空间以保存参数、返回地址以及临时变量，而往栈中压入数据和弹出数据都需要时间。->效率

2.递归中很多计算都是重复的，由于其本质是把一个问题分解成两个或者多个小问题，多个小问题存在相互重叠的部分，则存在重复计算，如fibonacci斐波那契数列的递归实现。->效率

3.调用栈可能会溢出，其实每一次函数调用会在内存栈中分配空间，而每个进程的栈的容量是有限的，当调用的层次太多时，就会超出栈的容量，从而导致栈溢出。->性能

## References
1. [理解「递归」与「动态规划」](https://juejin.im/post/5c2308abf265da615304ce41)