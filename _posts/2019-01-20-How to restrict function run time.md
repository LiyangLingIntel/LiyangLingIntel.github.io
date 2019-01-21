---
layout:     post
title:      "How to restrict python function run time"
subtitle:    怎样限制
date:       2019-01-20
author:     Lyon Ling
header-img: img/post-bg-runtimedecorator.png
catalog: true
tags:
    - Python
    - Tricks
    - Programing 
    - Decorator
---

>Nowadays, I'm dealing with a NLP project, there is a step which is about python regular expression and I have a problem.
>
>When program meets texts with some formats that my regular expression cannot match well, it will take really long time to finish or even get endless loop. So I want to set a run time upper limit onto each loop. Python decorator could solve this well.

### First method:

```python
import signal

def set_timeout(num, callback):
    def wrap(func):
        def handle(signum, frame):
            """
            callback function, 
            :signum: number of signal，
            :frame: the interrupted stack frame.
            """
            raise RuntimeError

        def to_do(*args, **kwargs):
            try:
                signal.signal(signal.SIGALRM, handle)  # set signal and callback func
                signal.alarm(num)  # set alarm for num second
                print('start alarm signal.')
                r = func(*args, **kwargs)
                print('close alarm signal.')
                signal.alarm(0)  # close alarm
                return r
            except RuntimeError as e:
                callback()
        return to_do
    return wrap

@set_timeout(num_sec, callback_func)
def target_func():
    pass
```

Code's from：https://blog.csdn.net/DylanYuan/article/details/83583981 

### Second method:

The second way is not as powerful as the first one, but it's much simpler.

It's needed to use `pip` to add package `timeout_decorator` to out python environment first.

```python
from timeout_decorator import timeout

@timeout(num_sec)
def target_func():
	pass
```

