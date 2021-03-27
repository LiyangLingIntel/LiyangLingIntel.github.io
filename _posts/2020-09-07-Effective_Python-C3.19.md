---
layout:     post
title:     【E-Python】当函数返回多个值时永远不要解包超过3个变量
subtitle:  【译】Effective Python 第二版 Chapter3.19
date:       2020-09-07
author:     Leon Ling
header-style: text
catalog: true
tags:
    - Python
    - Tutorial
    - EffetivePython
---

> Chapter 3 Functions - Item 19 Never unpack more than three variables when functions return multiple values

Python 解包语法（详见 [Chapter1.6 更偏向使用解包而不是索引的方式做多重赋值](/2020/09/04/Effective_Python_C1-6)）的一个效果就是允许Python函数看上去一次返回了多个值。比如，我想得到一群鳄鱼的多个统计数据。给一个存储长度的列表， 我需要计算其中的最大长度和最小长度。所以，我写了一个返回两个值的简单函数来完成这件事：

```python
def get_stats(numbers):
    minimum = min(numbers)
    maximum = max(numbers)
    return minimum, maximum
lengths = [63, 73, 72, 60, 67, 66, 71, 61, 72, 70]
minimum, maximum = get_stats(lengths)  # Two return values
print(f'Min: {minimum}, Max: {maximum}')
>>>
Min; 60, Max: 73
```

这种方式的实现机制是多个值被放在一个大小为2的元组里被一起返回。然后通过分配两个变量来解包返回的元组。这里，我使用一个更简单的示例来说明解包语句和多重返回函数如何以相同的方式工作：

```python
first, second = 1, 2
assert first == 1
assert second == 2

def my_func():
    return 1, 2
first, second = my_func()

assert first == 1
assert second == 2
```

多重返回值也可以被用来全解包的星标表达式接收（详见 [Chapter2.13 更偏向使用全解包而不是切片]()）。比如说，我需要另一个函数来计算每个鳄鱼相对平均水平有多大。这个函数返回一个比率的列表，但是我可以用星标表达式来接受列表中间部分的形式，单独接收最大值和最小值：0

```python
def get_avg_ratio(numbers):
    average = sum(numbers) / len(numbers)
    scaled = [x / average for x in numbers]
    scaled.sort(reverse=True)
    return scaled
longest, *middle, shortest = get_avg_ratio(lengths)
print(f'Longest:  {longest:>4.0%}')
print(f'Shortest: {shortest:>4.0%}')
>>>
Longest:  108%
Shortest:  89%
```

现在想象一下项目的需求变了，我还需要知道鳄鱼长度的平均数和中位数，以及鳄鱼的总数。我可以通过扩展 `get_stats` 函数来计算这些统计数据并将他们放在结果元组中返回，然后再被调用函数解包：

```python
def get_stats(numbers):
    minimum = min(numbers)
    maximum = max(numbers)
    count = len(numbers)
    average = sum(numbers) / count
    
    sorted_numbers = sorted(numbers)
    middle = count // 2
    if count % 2 == 0:
    		lower = sorted_numbers[middle - 1]
        upper = sorted_numbersp[middle]
        median = (lower + upper) / 2
    else:
      	median = sorted_numbers[middle]

		return minimum, maximum, average, median, count
  
minimum, maximum, average, median, count = get_stats(lengths)

print(f'Min: {minimum}, Max: {maximum}')
print(f'Average: {average}, Median: {median}, Count: {count}')

>>>
Min: 60, Max: 73
Average: 67.5, Median: 68.5, Count 10
```

这段代码里有两个问题。首先，所有的返回值都是数值，所以这些数值太容易被意外打乱顺序（比如交换 `average` 和 `median` 的顺序），进而导致很难被定位的Bug。使用大量的返回值极易出错：

```python
# Correct: 
minimum, maximum, average, median, count = get_stats(lengths)
# Oops! Median and average swapped:
minimum, maximum, median, average, count = get_stats(lengths)
```

第二问题是，调用函数并给返回值解包会导致这行很长，因而可能需要用多种方式之一包裹它（因为PEP8风格规范；详见 [Chapter1.2 遵循PEP8风格规范](/2020/09/03/Effective_Python-C1.2/#表达式和声明)），这会损害代码可读性：

```python
minimum, maximum, average, median, count = get_stats(
  	lengths)
minimum, maximum, average, median, count = \ 
		get_stats(lengths)
(minimum, maximum, average, 
 median, count) = get_stats(lengths)
(minimum, maximum, average, median, count
		)= get_stats(lengths)
```

为了避免这些问题，你应该在从函数解包多个返回值时，绝不使用超过三个变量。这些值可以是来自三元组，两个变量和一个捕获所有值的星号表达式，也可以是更短的值。 如果你需要解包更多的返回值，最好定义一个轻量级类或命名元组（详见 [Chapter5.37 编写Class, 而不是嵌套多级内置类型]()），并让你的函数返回该实例。

#### 需要记住的

* 你可以将多个值放在一个元组中来使函数返回多个值，然后调用者就可以利用Python的解包语法捕获这些值。
* 函数返回的多个值也可以被捕获所有的星号表达式解包。
* 解包到四个甚至多个变量很容易出错，应该避免这种写法；作为替代，返回小型类或者命名元组的实例。

