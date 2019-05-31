---
layout:     post
title:      "Record a trap in Python dict"
subtitle:    Python里的坑 -- 字典
date:       2019-01-21
author:     Lyon Ling
header-img: img/post-bg-pythontricks.png
catalog: true
tags:
    - Python
    - Tricks
    - Programing 
    - Traps
---

> 之前做项目的时候需要做python字典的初始化，所以在网上找了一些方法，没想到是有问题的，最近写作业的时候一下被坑惨了，特此一记。

具体代码如下：

```python
li = [1,2,3,4,5]
di = dict.fromkeys(li, [])
```

目的很简单，我想做一个以1-5为key，然后空列表为value的字典，然后以后再慢慢分别往里放内容。

可是万万没想到！

这里的`[]`是指向了一个固定的地址，that means，后面我们不论对`di[i]`的哪一个列表操作，最终都会改动到所有的list！

这是我很疑惑，这样的语法得分意义是什么，或者说不能用来动态制定value，只能建立一个固定值。

正确的操作：

```python
li = [1,2,3,4,5]
di = {}
for l in li:
	di.setdefault(l, [])
```

或者在for循环使用这个字典时，判断key `l`是否在字典中，然后再设置默认值（回归传统）。

所以说以后使用一些从网上找来的语法还是要小心，要仔细考虑背后的实现机制在采用。