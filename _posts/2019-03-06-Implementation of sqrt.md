---
layout:     post
title:      Implementation of sqrt
subtitle:   如何徒手开根号
date:       2019-03-07
author:     Lyon Ling
header-img: img/post-bg-sqrt.png
catalog: true
tags:
    - JobHunting
    - Math
---

> 最近听说同学去面试被问了如何实现求根函数，想起来之前看过一篇帖子《[sqrt函数实现(神奇的算法)](https://blog.csdn.net/qq_26499321/article/details/73724763)》，所以这里整理一下积累下来。

### 二分法

常规思路就是二分法通过预设的精度无限逼近正确结果。

```python
def sqrt_bisection(num, eps=1e-5):
    if num < 0:
        raise Exception('Input number should be non-negtive')
    lower = 0
    upper = num
    last_mid = 0
    mid = num / 2
    while(abs(mid-last_mid) > eps):
        if mid*mid > num:
            upper = mid
        else:
            lower = mid
        last_mid = mid
        mid = (upper + lower) / 2        
    return mid
```

### 牛顿迭代法

整体思路和二分法类似，也是通过迭代去逼近正确结果。首先代码如下：

```python
def sqrt_newton(num, eps=1e-5):
    if num < 0:
        raise Exception('Input number should be non-negtive')
    res = num
    last = 0
    while(abs(res-last) > eps):
        last = res
        res = (res + num/res) / 2
    return res
```

至于原理，也很简单。就是用$(x, f(x))$的切线来逼近方程 $x^2 = a$ 的根。根号$a$实际上就是$x^2-a=0$的一个正实根，这个函数的导数是$2x$。也就是说，函数上任一点$(x,f(x))$处的切线斜率是$2x$。那么，$x-f(x)/(2x)$就是一个比$x$更接近的近似值。

### 性能对比

这边测试一下性能，发现**牛顿迭代法**明显在迭代次数和计算时间上都会有很大的性能提升。

```
sqrt_bisection: 18 loops
sqrt_bisection: 1.7430782318115234

sqrt_newton: 5 loops
sqrt_newton: 0.3349781036376953
```

事实上原文中还提供了第三种方法：

基于牛顿迭代的思路，通过一个预先设定的常数去做计算平方根。这然这样的方法相比前两种常规方法，时间极大地缩短了。但是这里我在从原文cpp转python的过程中发现同样的算法在python并没有奏效，可能是两种语言内部编译机制的问题，或者是我的理解的问题，在彻底弄清楚前就暂时先不放上来了。