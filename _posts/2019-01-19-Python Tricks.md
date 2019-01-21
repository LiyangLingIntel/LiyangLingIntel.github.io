---
layout:     post
title:      "Python Tricks"
subtitle:    Python 里的奇技淫巧 [持续更新]
date:       2019-01-19
author:     Lyon Ling
header-img: img/post-bg-pythontricks.png
catalog: true
tags:
    - Python
    - Tricks
    - Programing 
---

>When we do python programing, I find there are many tricks and skills to make our code more pythonic and more effectively.
>
>Here I collect them, and keep this post continuously updated.



## String Operation

### F string formatting

The Official name of this kind of grammar is _Literal String Interpolation_, a new characteristic in python3.6. Here are some examples.

```python
name = "Eric"
age = 74
f"Hello, {name}. You are {age}."
# output: 
# Helle, Eric. You are 74.
F"Hello, {name}. You are {age}."
# capital F is okay.
```

You can put any expression in the { }. Even you can write a sentence in several lines.

```python
# type 1
message = (f"Hi {name}. "
        f"You are a {profession}. "
        f"You were in {affiliation}.")
# output: 'Hi Eric. You are a comedian. You were in Monty Python.'

# type 2
message = (f"Hi {name}. "
        "You are a {profession}. "
        "You were in {affiliation}.")
# output: 'Hi Eric. You are a comedian. You were in Monty Python.'

# type 3
message = f"""
    Hi {name}. 
    You are a {profession}. 
    You were in {affiliation}.
 """
 # output: '\n    Hi Eric. \n    You are a comedian. \n    You were in Monty Python.\n '
```

如果`!, :`或`}`不在括号，大括号，括号或字符串中，则它将被解释为表达式的结尾。 可以通过将这些符号嵌套在圆括号中来解决此问题。

### How to distinguish number / alphabet in string

```python
str_1 = "123"
str_2 = "Abc"
str_3 = "123Abc"

#用isdigit函数判断是否数字
print(str_1.isdigit())
Ture
print(str_2.isdigit())
False
print(str_3.isdigit())
False

#用isalpha判断是否字母
print(str_1.isalpha())    
False
print(str_2.isalpha())
Ture    
print(str_3.isalpha())    
False

#isalnum判断是否数字和字母的组合
print(str_1.isalnum())    
Ture
print(str_2.isalnum())
Ture
print(str_1.isalnum())    
Ture
```

*Tips: If string contains other characters, like space, `False` will also be returned.*



## Advanced Functions

- **lambda**

  Anonymous fuction

  ```python
  lambda x,y:x+y
  lambda argument_list: expresstion
  '''
  argument list is like common python functions, like
  "a, b / a=1, b=2 / *args / **kwargs /a, b=1, *args" are all okay
  expression means the return, it's needed to mention that expression must be in one line
  '''
  ```

- **map(function,  sequence)**

  ```python
  def f(x):
      return x**2
  map(f, range(10))
  '''
  f: ONE-variable function
  map() means apply function f to each element of the sequence, and return a new sequence
  '''
  ```

- **reduce(function, sequence)**

  ```python
  def f(x, y):
      return x+y 
  reduce(f, range(10))
  '''
  f: TWO-variable function
  reduce() means that apply f to result of first two elements from f and the next element iteractively
  reduce(f, [x1,x2,x3,x4]) = f(f(f(x1,x2),x3),x4)
  '''
  ```

- **filter(function, sequence)**

  ```python
  def is_odd(s):
      return n%2==1
  filter(is_odd, range(10))
  # output: [1,3,5,7,9]
  '''
  f: filter function, return True or False
  filter() means that we apply f to each element of sequence and have judgements respectively, if false, drop that element and keep the true, return filtered sequence finally.
  '''
  ```



## Decorator

__Example first:__

```python
def dec1(func):  # decorator
    print("1111")  
    def one():   # wrapper
        print("2222")  
        func()  
        print("3333")  
    return one  

def dec2(func):  
    print("aaaa")  
    def two():  
        print("bbbb")  
        func()  
        print("cccc")  
    return two  

@dec1  
@dec2  
def test():  
    print("test test")  

if __name__ == '__main__':
    test()
```

**Output:**

```
aaaa  
1111  
2222  
bbbb  
test test  
cccc  
3333
```

__Meanwhile__:

```python
@dec1
def test1():
	print('test')

def test2():
	print('test')
test2 = dec1(test2)

test1()
test2()

# test1() and test2() have the same output
```

多个decorator的执行顺序是从最后一个开始，直到第一个装饰器，再顺序执行wrapper，以及被装饰的function。

引入decorator装饰器的目的是为了__动态的给函数加上功能__，通过代码复用完成对函数的修改，减少对函数本身代码的反复修改。

### Common Decorator

`@property`：将一个类的方法变成类属性去调用

```python
class Student(object):

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
    
    @property
    def gpa(self):
        if score > 85:
            return 4
        elif score > 75:
            return 3
        elif score > 65:
            return 2
        else:
            return 0
```

把一个getter方法变成属性，只需要加上`@property`就可以了，此时，`@property`本身又创建了另一个装饰器`@score.setter`，负责把一个setter方法变成属性赋值，于是，我们就拥有一个可控的属性操作。只定义getter方法，不定义setter方法就是一个只读属性 。



