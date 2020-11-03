---
layout:     post
title:      Python Interview Questions
subtitle:   Python面试题
date:       2019-03-06
author:     Lyon Ling
header-img: img/post-bg-jubhunt2.jpg
catalog: true
tags:
    - Python
    - JobHunting
    - 转载
---

> 本文主要内容转载自[**kenwoodjw**](https://github.com/kenwoodjw)的`GitHub` [repo](https://github.com/kenwoodjw/python_interview_question), 因为原文GitHub中没有可以跳转目录索引所以转载过来重新生成目录，方便阅读。
>
> 转载过程中对其中的一些代码细节做了一些微小的改动，有的地方也加入一些自己的理解。整篇内容比较基础，适合用来对python开发做一个基本的回顾。

[TOC]

## Python基础

### 1.1 有一个jsonline格式的文件file.txt 大小约为10K

```python
def get_lines():
    l = []
    with open('file.txt','rb) as f:
        for eachline in f:
            l.append(eachline)
        return l

if __name__ == '__main__':
    for e in get_lines():
        process(e) #处理每一行数据
```
现在要处理一个大小为10G的文件，但是内存只有4G，如果在只修改get_lines 函数而其他代码保持不变的情况下，应该如何实现？需要考虑的问题都有那些？
```python
def get_lines():
    l = []
    with open('file.txt','rb') as f:
        data = f.readlines(60000)
    l.append(data)
    yield l
```
要考虑的问题有：

* 内存只有4G无法一次性读入10G文件，需要分批读入分批读入数据要记录**每次读入数据的位置**。
* 分批每次读取数据的大小，太小会在**读取操作**花费过多时间。

### 1.2 补充缺失的代码

```python
def print_directory_contents(sPath):
    """
    这个函数接收文件夹的名称作为输入参数
    返回该文件夹中文件的路径
    以及其包含文件夹中文件的路径
    """
    import os
    for sChild in os.listdir(sPath):
        sChildPath = os.path.join(sPath,sChild)
        if os.path.isdir(sChildPath):
            print_directory_contents(sChildPath)
        else:
            print(sChildPath)
```


## 模块与包

### 2.1 输入日期， 判断这一天是这一年的第几天？

```python
import datetime
def dayofyear():
    year = input("请输入年份: ")
    month = input("请输入月份: ")
    day = input("请输入天: ")
    date1 = datetime.date(year=int(year),month=int(month),day=int(day))
    date2 = datetime.date(year=int(year),month=1,day=1)
    return (date1-date2).days+1
```
### 2.2 打乱一个排好序的list对象alist？

```python
import random
alist = [1,2,3,4,5]
random.shuffle(alist)
print(alist)
```


## 数据类型

### 3.1 现有字典 d= {'a':24,'g':52,'i':12,'k':33}请按value值进行排序?

```python
sorted(d.items(), key=lambda x: x[1])
```
### 3.2 字典推导式

```python
d = {key:value for (key,value) in iterable}
```
### 3.3 请反转字符串 "aStr"?

```python
print("aStr"[::-1])
```
### 3.4 将字符串 "k:1 |k1:2|k2:3|k3:4"，处理成字典 {k:1,k1:2,...}

```python
str1 = "k:1|k1:2|k2:3|k3:4"
def str2dict(str1):
    dict1 = {}
    for iterms in str1.split('|'):
        key,value = iterms.split(':'):
            dict1[key] = value
    return dict1
```
### 3.5 请按alist中元素的age由小到大排序

```python
alist = [{'name':'a','age':20},{'name':'b','age':30},{'name':'c','age':25}]
def sort_by_age(list1):
    return sorted(alist,key=lambda x:x['age'],reverse=True)
```
### 3.6 下面代码的输出结果将是什么？

```python
list = ['a','b','c','d','e']
print(list[10:])
```
代码将输出[],不会产生IndexError错误，就像所期望的那样，尝试用超出成员的个数的index来获取某个列表的成员。例如，尝试获取list[10]和之后的成员，会导致IndexError。然而，尝试获取列表的**切片**，开始的index超过了成员个数**不会产生IndexError**，而是仅仅返回一个空列表。这成为特别让人恶心的疑难杂症，因为运行的时候没有错误产生，导致Bug很难被追踪到。
### 3.7 写一个列表生成式，产生一个公差为11的等差数列

```python
print([x*11 for x in range(10)])
print([x for x in range(0, 100, 11)])
```
### 3.8 给定两个列表，怎么找出他们相同的元素和不同的元素？

```python
list1 = [1,2,3]
list2 = [3,4,5]
set1 = set(list1)
set2 = set(list2)
print(set1 & set2)
print(set1 ^ set2)
```
### 3.9 请写出一段python代码实现删除list里面的重复元素？

```python
l1 = ['b','c','d','c','a','a']
l2 = list(set(l1))
print(l2)
# 这里有个问题，是通过list->set->list几步抓换会改变原有list的元素顺序，所有有下面的改动
```
用list类的sort方法:
```python
l1 = ['b','c','d','c','a','a']
l2 = list(set(l1))
l2.sort(key=l1.index)
print(l2)
```
也可以这样写:
```python
l1 = ['b','c','d','c','a','a']
l2 = sorted(set(l1),key=l1.index)
print(l2)
```
也可以用遍历：
```python
l1 = ['b','c','d','c','a','a']
l2 = []
for i in l1:
    if not i in l2:
        l2.append(i)
print(l2)
```


## 企业面试题

### 4.1 python新式类和经典类的区别？

1. 在python里凡是继承了object的类，都是新式类
2. Python3里只有新式类
3. Python2里面继承object的是新式类，没有写父类的是经典类
4. 经典类目前在Python里基本没有应用

### 4.2 python中内置的数据结构有几种？

1. 整型 int、 长整型 long、浮点型 float、 复数 complex
2. 字符串 str、 列表list、 元祖tuple
3. 字典 dict 、 集合 set

### 4.3 python如何实现单例模式?请写出两种实现方式?`

> **单例模式**（Singleton Pattern）原本是Java开发的基本设计模式之一，属于**创建型**模式。这种**模式**涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。 这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要**实例**化该类的对象。

第一种方法:使用**装饰器**
```python
def singleton(cls):
    # 这种方法下保证每个被装饰的class只有一个实例对象
    instances = {}
    def wrapper(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return wrapper

@singleton
class Foo(object):
    pass

foo1 = Foo()
foo2 = Foo()
print(foo1 is foo2) #True
```
第二种方法：使用**基类**
New 是真正创建实例对象的方法，所以重写基类的new 方法，以此保证创建对象的时候只生成一个实例

```python
class Singleton(object):
    def __new__(cls,*args,**kwargs):
        if not hasattr(cls,'_instance'):
            cls._instance = super(Singleton,cls).__new__(cls,*args,**kwargs)
        return cls._instance

class Foo(Singleton):
    pass

foo1 = Foo()
foo2 = Foo()

print foo1 is foo2 #True
```
第三种方法：**元类**，元类是用于创建类对象的类，类对象创建实例对象时一定要调用call方法，因此在调用call时候保证始终只创建一个实例即可，type是python的元类
```python
class Singleton(type):
    def __call__(cls,*args,**kwargs):
        if not hasattr(cls,'_instance'):
            cls._instance = super(Singleton,cls).__call__(*args,**kwargs)
        return cls._instance
```
```python
class Foo(object):
    __metaclass__ = Singleton

foo1 = Foo()
foo2 = Foo()
print foo1 is foo2 #True

```
### 4.4 反转一个整数，例如-123 --> -321 

```python
class Solution(object):
    def reverse(self,x):
        if -10<x<10:
            return x
        str_x = str(x)
        if str_x[0] !="-":
            str_x = str_x[::-1]
            x = int(str_x)
        else:
            str_x = str_x[1:][::-1]
            x = int(str_x)
            x = -x
        return x if -2147483648<x<2147483647 else 0
    
if __name__ == '__main__':
    s = Solution()
    reverse_int = s.reverse(-120)
    print(reverse_int)
```
## 4.5 设计实现遍历目录与子目录，抓取.pyc文件
第一种方法：
```python
import os

def getFiles(dir,suffix):
    res = []
    for root,dirs,files in os.walk(dir):
        for filename in files:
            name,suf = os.path.splitext(filename)
            if suf == suffix:
                res.append(os.path.join(root,filename))

    print(res)

getFiles("./",'.pyc')
```
第二种方法：
```python
import os

def pick(obj):
    try:
        if obj.[-4:] == ".pyc":
            print(obj)
        except:
            return None

def scan_path(ph):
    file_list = os.listdir(ph)
    for obj in file_list:
        if os.path.isfile(obj):
    		pick(obj)
        elif os.path.isdir(obj):
            scan_path(obj)

if __name__=='__main__':
    path = input('输入目录')
    scan_path(path)
```
> 上面两种方法主要采用了os库的`listdir`和`walk`的方法，但是我觉得如果换成我来写可能会写的更简洁一点。

```python
import os

def get_files(dir, suffix):
    # 需要在函数外验证dir的有效性
    res = []
    for root, dirs, files in os.walk(dir):
		file_paths = [os.path.join(root, file) for file in files if file.endswith(suffix)]
        res.extend(file_paths)
    return res
```

### 4.6 一行代码实现1-100之和

```python
count = sum(range(0,101))
```



## Python高级

### 5.1 对设计模式的理解，简述你了解的设计模式？

设计模式是经过总结，优化的，对我们经常会碰到的一些编程问题的可重用解决方案。一个设计模式并不像一个类或一个库那样能够直接作用于我们的代码，反之，设计模式更为高级，它是一种必须在特定情形下实现的一种**方法模板**。
常见的是**工厂模式**和**单例模式**。

> **工厂模式**（Factory Pattern）是 Java 中最常用的设计模式之一。这种类型的设计模式属于**创建型**模式，它提供了一种创建对象的最佳方式。在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

下面是一个简单的工厂类的示例

```java
// 子类实现如下
public interface Shape {
    void draw();
}
public class CircleShape implements Shape {
    public CircleShape() {
        System.out.println(  "CircleShape: created");
    }
    @Override
    public void draw() {
        System.out.println(  "draw: CircleShape");
    }
}

public class ShapeFactory {
    // 工厂类
    // 其中每个子类需要单独实现
    public static final String TAG = "ShapeFactory";
    public static Shape getShape(String type) {
        Shape shape = null;
        if (type.equalsIgnoreCase("circle")) {
            shape = new CircleShape();
        } else if (type.equalsIgnoreCase("rect")) {
            shape = new RectShape();
        } else if (type.equalsIgnoreCase("triangle")) {
            shape = new TriangleShape();
        }
        return shape;
    }
}
```

> [**单例模式**](###4.3 python如何实现单例模式?请写出两种实现方式?)上面提过，就不再赘述。

### 5.2 请手写一个单例

```python
# python2
class Singleton(object):
    __instance = None
    def __new__(cls,*args,**kwargs):
        if cls.__instance is None:
            cls.__instance = objecet.__new__(cls)
            return cls.__instance
        else:
            return cls.__instance
```
### 5.3 单例模式的应用场景有那些？

单例模式应用的场景一般发现在以下条件下：
资源共享的情况下，避免由于资源操作时导致的性能或损耗等，如日志文件，应用配置。
控制资源的情况下，方便资源之间的互相通信。如线程池等，1,网站的计数器 2,应用配置 3.多线程池 4数据库配置 数据库连接池 5.应用程序的日志应用...

### 5.4 对装饰器的理解，并写出一个计时器记录方法执行性能的装饰器？

装饰器本质上是一个python函数，它可以让其他函数在不需要做任何代码变动的前提下增加额外功能，装饰器的返回值也是一个函数对象。

```python
import time
def timeit(func):
    def wrapper():
        start = time.clock()
        func()
        end = time.clock()
        print('used:',end-start)
        return wrapper
@timeit
def foo():
    print('in foo()'foo())
```
### 5.5 解释一下什么是闭包(Closure)？

在函数内部再定义一个函数，并且这个函数用到了外边函数的变量，那么将这个函数以及用到的一些变量称之为闭包。

### 5.6 函数装饰器有什么作用？

装饰器本质上是一个python函数，它可以在让其他函数在不需要做任何代码的变动的前提下增加额外的功能。装饰器的返回值也是一个函数的对象，它经常用于有切面需求的场景。比如：插入日志，性能测试，事务处理，缓存。权限的校验等场景，有了装饰器就可以抽离出大量的与函数功能本身无关的雷同代码并发并继续使用。
### 5.7 生成器，迭代器的区别？

迭代器（iterator）是一个更抽象的概念，任何对象，如果它的类有`next`方法和`iter`方法返回自己本身，对于string,list,dict,tuple等这类容器对象，使用for循环遍历是很方便的，在后台for语句对容器对象调用iter()函数，iter()是python的内置函数，`iter()`会返回一个定义了`next()`方法的迭代器对象，它在容器中逐个访问容器内元素，`next()`也是python的内置函数，在没有后续元素时，`next()`会抛出一个`StopIteration`异常。
生成器（Generator）是创建迭代器的简单而强大的工具。它们写起来就像是正规的函数，只是在需要返回数据的时候使用`yield`语句。每次`next()`被调用时，生成器会返回它脱离的位置（它记忆语句最后一次执行的位置和所有的数据值）
区别： 生成器能做到迭代器能做的所有事，而且因为自动创建`iter()`和`next()`方法，生成器显得特别简洁，而且生成器也是高效的，使用生成器表达式取代列表解析可以同时节省内存。除了创建和保存程序状态的自动方法，当发生器终结时，还会自动抛出`StopIteration`异常。

### 5.8 X是什么类型?

```python
X = (for i in ramg(10))
# X 是generator类型
```
### 5.9 请用一行代码 实现将1-N 的整数列表以3为单位分组

```python
# original one
print([[x for x in range(1,100)] [i:i+3] for i in range(0,len(list_a),3)])
# My idea
print([[i,i+1,i+2] for i in range(1, 100, 3)])
```
### 5.10 Python中yield的用法

`yield`就是保存当前程序执行状态。你用for循环的时候，每次取一个元素的时候就会计算一次。用yield的函数叫`generator`,和`iterator`一样，它的好处是不用一次计算所有元素，而是用一次算一次，可以节省很多空间，generator每次计算需要上一次计算结果，所以用yield, 否则一return，上次计算结果就没了



## 系统编程

### 6.1 进程总结

**进程**：程序运行在操作系统上的一个实例，就称之为进程。进程需要相应的系统资源：内存、时间片、pid。**进程是操作系统资源调度的最小单位**。

* **创建进程**：

  首先要导入multiprocessing中的Process：

  创建一个Process对象;
  创建Process对象时，可以传递参数;

```pseudocode
    p = Process(target=XXX,args=(tuple,),kwargs={key:value})
    target = XXX 指定的任务函数，不用加(),
    args=(tuple,)kwargs={key:value} 给任务函数传递的参数
```
* **使用start()启动进程**:
  结束进程
  给子进程指定函数传递参数Demo

```python
    import os
    from mulitprocessing import Process
    import time

    def pro_func(name,age,**kwargs):
        for i in range(5):
            print("子进程正在运行中，name=%s,age=%d,pid=%d"%(name,age,os.getpid()))
            print(kwargs)
            time.sleep(0.2)
    if __name__ =="__main__":
        #创建Process对象
        p = Process(target=pro_func,args=('小明',18),kwargs={'m':20})
        #启动进程
        p.start()
        time.sleep(1)
        #1秒钟之后，立刻结束子进程
        p.terminate()
        p.join()
```
*注意：进程间不共享全局变量*

* 进程之间的通信: `Queue`

* 在初始化`Queue()`对象时（例如q=Queue(),若在括号中没有指定最大可接受的消息数量，获数量为负值时，那么就代表可接受的消息数量没有上限一直到内存尽头）

  * `Queue.qsize()`: 返回当前队列包含的消息数量

  * `Queue.empty()`: 如果队列为空，返回True，反之False

  * `Queue.full()`: 如果队列满了，返回True,反之False

  * `Queue.get([block[,timeout]])`: 获取队列中的一条消息，然后将其从队列中移除，block默认值为True。
    如果block使用默认值，且没有设置timeout(单位秒),消息队列如果为空，此时程序将被**阻塞**（停在读取状态），直到消息队列读到消息为止，如果设置了timeout，则会等待timeout秒，若还没读取到任何消息，则抛出"Queue.Empty"异常。

  * `Queue.get_nowait()`: 相当于`Queue.get(False)`

  * `Queue.put(item,[block[,timeout]])`: 将item消息写入队列，block默认值为True;
    如果block使用默认值，且没有设置timeout（单位秒），消息队列如果已经没有空间可写入，此时程序将被**阻塞**（停在写入状态），直到从消息队列腾出空间为止，如果设置了timeout，则会等待timeout秒，若还没空间，则抛出”Queue.Full"异常。
    如果block值为False，消息队列如果没有空间可写入，则会立刻抛出"Queue.Full"异常。

  * Queue.put_nowait(item): 相当`Queue.put(item,False)`

    进程间通信Demo:

```python
    from multiprocessing import Process, Queue
    import os,time,random
    # 写数据进程执行的代码：
    def write(q):
        for value in ['A','B','C']:
            print("Put %s to queue...",%value)
            q.put(value)
            time.sleep(random.random())
    # 读数据进程执行的代码
    def read(q):
        while True:
            if not q.empty():
                value = q.get(True)
                print("Get %s from queue.",%value)
                time.sleep(random.random())
            else:
                break
    if __name__=='__main__':
        #父进程创建Queue，并传给各个子进程
        q = Queue()
        pw = Process(target=write,args=(q,))
        pr = Process(target=read,args=(q,))
        #启动子进程pw ，写入：
        pw.start()
        #等待pw结束
        pw.join()
        #启动子进程pr，读取：
        pr.start()
        pr.join()
        #pr 进程里是死循环，无法等待其结束，只能强行终止:
        print('')
        print('所有数据都写入并且读完')
```
* 进程池 Pool

```python
    #coding:utf-8
    from multiprocessing import Pool
    import os,time,random

    def worker(msg):
        t_start = time.time()
        print("%s 开始执行，进程号为%d"%(msg,os.getpid()))
        # random.random()随机生成0-1之间的浮点数
        time.sleep(random.random()*2)
        t_stop = time.time()
        print(msg,"执行完毕，耗时%0.2f”%（t_stop-t_start))

    po = Pool(3) # 定义一个进程池，最大进程数3
    for i in range(0,10):
        po.apply_async(worker,(i,))
        print("---start----")
        po.close()
        po.join()
        print("----end----")
```
* 进程池中使用 Queue
  如果要使用Pool创建进程，就需要使用`multiprocessing.Manager()`中的`Queue()`,而不是`multiprocessing.Queue()`,否则会得到如下的错误信息：
  `RuntimeError`： Queue objects should only be shared between processs through inheritance

```python
        from multiprocessing import Manager,Pool
        import os,time,random
        def reader(q):
            print("reader 启动(%s),父进程为（%s)"%(os.getpid(),os.getpid()))
            for i in range(q.qsize()):
                print("reader 从Queue获取到消息:%s"%q.get(True))

        def writer(q):
            print("writer 启动（%s),父进程为(%s)"%(os.getpid(),os.getpid()))
            for i ini "itcast":
                q.put(i)
        if __name__ == "__main__":
            print("(%s)start"%os.getpid())
            q = Manager().Queue()#使用Manager中的Queue
            po = Pool()
            po.apply_async(wrtier,(q,))
            time.sleep(1)
            po.apply_async(reader,(q,))
            po.close()
            po.join()
            print("(%s)End"%os.getpid())
```
### 6.2 谈谈你对多进程，多线程，以及协程的理解，项目是否用？

这个问题被问的概念相当之大，
**进程**：一个运行的程序（代码）就是一个进程，没有运行的代码叫程序，**进程是系统资源分配的最小单位**，进程拥有自己独立的内存空间，所有进程间数据不共享，开销大。
**线程**: **cpu调度执行的最小单位**，也叫执行路径，不能独立存在，依赖进程存在，一个进程至少有一个线程，叫主线程，而多个线程共享内存（数据共享，共享全局变量），从而极大地提高了程序的运行效率。
**协程**: 是一种<u>用户态</u>的轻量级线程，**<u>协程的调度完全由程序员控制(系统内核不管)</u>**。协程拥有自己的寄存器上下文和栈。协程调度时，将寄存器上下文和栈保存到其他地方，在切回来的时候，恢复先前保存的寄存器上下文和栈，直接操纵栈则基本没有内核切换的开销，可以不加锁的访问全局变量，所以<u>上下文的切换非常快</u>。

### 6.3 Python异常使用场景有那些？

异步的使用场景:
1、 不涉及共享资源，获对共享资源只读，即非互斥操作
2、 没有时序上的严格关系
3、 不需要原子操作，或可以通过其他方式控制原子性
4、 常用于IO操作等耗时操作，因为比较影响客户体验和使用性能
5、 不影响主线程逻辑

### 6.4 多线程共同操作同一个数据互斥锁同步？

```python
    import threading
    import time
    class MyThread(threading.Thread):
        def run(self):
            global num
            time.sleep(1)
        
            if mutex.acquire(1):
                num +=1
                msg = self.name + 'set num to ' + str(num)
                print(msg)
                mutex.release()
    num = 0
    mutex = threading.Lock()
    def test():
        for i in range(5):
            t = MyThread()
            t.start()
    if __name__=="__main__":
        test()
```
### 6.5 什么是多线程竞争？

* 线程是非独立的，同一个进程里线程是数据共享的，当各个线程访问数据资源时会出现竞争状态即：数据几乎同步会被多个线程占用，造成数据混乱，即所谓的线程不安全
* 那么怎么解决多线程竞争问题？—— 锁
* 锁的好处： 确保了某段关键代码（共享数据资源）只能由一个线程从头到尾完整地执行能解决多线程资源竞争下的原子操作问题。
* 锁的坏处：阻止了多线程并发执行，包含锁的某段代码实际上只能以**单线程模式**执行，效率就大大地下降了
* 锁的致命问题：死锁

### 6.6  请介绍一下Python的线程同步？

1. **setDaemon(False)**
   当一个进程启动之后，会默认产生一个主线程，因为线程是程序执行的最小单位，当设置多线程时，主线程会创建多个子线程，在Python中，默认情况下就是setDaemon(False),主线程执行完自己的任务以后，就退出了，此时子线程会继续执行自己的任务，直到自己的任务结束。
   例子

```python
    import threading 
    import time
    
    def thread():
        time.sleep(2)
        print('---子线程结束---')
    
    def main():
        t1 = threading.Thread(target=thread)
        t1.start()
        print('---主线程--结束')
    
    if __name__ =='__main__':
        main()
        
    #执行结果
    ---主线程--结束
    ---子线程结束---
```
2. **setDaemon（True)**
   当我们使用setDaemon(True)时，这是子线程为守护线程，主线程一旦执行结束，则全部子线程被强制终止
   例子

```python
    import threading
    import time
    def thread():
        time.sleep(2)
        print(’---子线程结束---')
    def main():
        t1 = threading.Thread(target=thread)
        t1.setDaemon(True) #设置子线程守护主线程
        t1.start()
        print('---主线程结束---')
    
    if __name__ =='__main__':
        main()
    #执行结果
    ---主线程结束--- #只有主线程结束，子线程来不及执行就被强制结束
```
3. **join（线程同步)**
   join 所完成的工作就是线程同步，即主线程任务结束以后，进入堵塞状态，一直等待所有的子线程结束以后，主线程再终止。
   当设置守护线程时，含义是主线程对于子线程等待timeout的时间将会杀死该子线程，最后退出程序，所以说，如果有10个子线程，全部的等待时间就是每个timeout的累加和，简单的来说，就是给每个子线程一个timeou的时间，让他去执行，时间一到，不管任务有没有完成，直接杀死。
   没有设置守护线程时，主线程将会等待timeout的累加和这样的一段时间，时间一到，主线程结束，但是并没有杀死子线程，子线程依然可以继续执行，直到子线程全部结束，程序退出。
   例子

```python
    import threading
    import time

    def thread():
        time.sleep(2)
        print('---子线程结束---')
    
    def main():
        t1 = threading.Thread(target=thread)
        t1.setDaemon(True)
        t1.start()
        t1.join(timeout=1)#1 线程同步，主线程堵塞1s 然后主线程结束，子线程继续执行
                          #2 如果不设置timeout参数就等子线程结束主线程再结束
                          #3 如果设置了setDaemon=True和timeout=1主线程等待1s后会强制杀死子线程，      
                          #  然后主线程结束
        print('---主线程结束---')
    
    if __name__=='__main___':
        main()
```
### 6.7 解释以下什么是锁，有哪几种锁？

**锁(Lock)**是python提供的对线程控制的对象。有互斥锁，可重入锁，死锁。

### 6.8 什么是死锁？

若干子线程在系统资源竞争时，都在等待对方对某部分资源解除占用状态，结果是谁也不愿先解锁，互相干等着，程序无法执行下去，这就是死锁。
GIL锁 全局解释器锁（只在cython里才有）
作用： 限制多线程同时执行，保证同一时间只有一个线程执行，所以cython里的多线程其实是伪多线程！
所以<u>python里常常使用协程技术来代替多线程</u>，协程是一种更轻量级的线程。
进程和线程的切换时由系统决定，而协程由我们程序员自己决定，而模块gevent下切换是遇到了耗时操作时才会切换
三者的关系：进程里有线程，线程里有协程。

### 6.9 多线程交互访问数据，如果访问到了就不访问了？

怎么避免重读？
创建一个已访问数据列表，用于存储已经访问过的数据，并加上互斥锁，在多线程访问数据的时候先查看数据是否在已访问的列表中，若已存在就直接跳过。

### 6.10 什么是线程安全，什么是互斥锁？

每个对象都对应于一个可称为’*互斥锁*‘的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象。
同一进程中的多线程之间是共享系统资源的，多个线程同时对一个对象进行操作，一个线程操作尚未结束，另一线程已经对其进行操作，导致最终结果出现错误，此时需要对被操作对象添加互斥锁，保证每个线程对该对象的操作都得到正确的结果。

### 6.11说说下面几个概念：同步，异步，阻塞，非阻塞？

同步： 多个任务之间有先后顺序执行，一个执行完下个才能执行。
异步： 多个任务之间没有先后顺序，可以同时执行，有时候一个任务可能要在必要的时候获取另一个同时执行的任务的结果，这个就叫回调！
阻塞： 如果卡住了调用者，调用者不能继续往下执行，就是说调用者阻塞了。
非阻塞： 如果不会卡住，可以继续执行，就是说非阻塞的。
同步异步相对于**多任务**而言，阻塞非阻塞相对于**代码执行**而言。

### 6.12 什么是僵尸进程和孤儿进程？怎么避免僵尸进程？

* **孤儿进程**： 父进程退出，子进程还在运行的这些子进程都是孤儿进程，孤儿进程将被init 进程（进程号为1）所收养，并由init 进程对他们完成状态收集工作。
* **僵尸进程**： 进程使用fork 创建子进程，如果子进程退出，而父进程并没有调用wait 获waitpid 获取子进程的状态信息，那么子进程的进程描述符仍然保存在系统中的这些进程是僵尸进程。
* 避免僵尸进程的方法：
  * fork 两次用孙子进程去完成子进程的任务
  * 用wait()函数使父进程阻塞
  * 使用信号量(semaphore)，在signal handler 中调用waitpid, 这样父进程不用阻塞

### 7.13 python中进程与线程的使用场景？

多进程适合在CPU密集操作（cpu操作指令比较多，如位多的的浮点运算）。
多线程适合在IO密性型操作（读写数据操作比多的的，比如爬虫）

### 7.14 线程是并发还是并行，进程是并发还是并行？

**线程是并发，进程是并行**。
进程之间互相独立，是系统分配资源的最小单位，同一个线程中的所有线程共享资源。

### 7.15 并行(parallel)和并发（concurrency)?

**并行**： 同一时刻多个任务同时在运行
**并发**:  在同一时间间隔内多个任务都在运行，但是并不会在同一时刻同时运行，存在交替执行的情况。
实现并行的库有：`multiprocessing`
实现并发的库有:  `threading`
程序需要执行较多的读写、请求和回复任务的需要大量的IO操作，IO密集型操作使用并发更好。
CPU运算量大的程序，使用并行会更好。

### 7.16 IO密集型和CPU密集型区别？

**IO密集型**： 系统运行，大部分的状况是CPU在等 I/O（硬盘/内存）的读/写
**CPU密集型**： 大部分时间用来做计算，逻辑判断等CPU动作的程序称之CPU密集型。