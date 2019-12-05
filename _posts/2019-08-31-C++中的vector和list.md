---
layout:     post
title:      "C++ std中vector和list的区别"
subtitle:   从零开始C++
date:       2019-08-31
author:     Lyon Ling
header-img: img/post-bg-spark.png
catalog: true
tags:
    - C++
    - Foundation
---

[TOC]

### 1. 数据结构设计

* vector数据结构

  vector和数组类似，拥有一段连续的内存空间，并且起始地址不变。
  因此能高效的进行随机存取，时间复杂度为o(1);
  但因为内存空间是连续的，所以在进行插入和删除操作时，会造成内存块的拷贝，时间复杂度为o(n)。
  另外，当数组中内存空间不够时，会重新申请一块内存空间并进行内存拷贝。

* list数据结构

  list是由双向链表实现的，因此内存空间是不连续的。
  只能通过指针访问数据，所以list的随机存取非常没有效率，时间复杂度为o(n);
  但由于链表的特点，能高效地进行插入和删除。

### 2. 区别和联系

用一个简单的vector和list使用示例说明：

```cpp
#include<iostream>
#include<vector>
#include<list>
using namespace std;
int main()
{
    vector<int> v;
    list<int> l;
    for(int i=0;i<8;i++) ////往v和l中分别添加元素
    {
        v.push_back(i);
        l.push_back(i);
    }
    cout<<"v[2]="<<v[2]<<endl;
    //cout<<"l[2]="<<l[2]<<endl;  			//编译错误,list没有重载[]
    cout<<(v.begin()<v.end())<<endl; 
    //cout<<(l.begin()<l.end())<<endl; 	/编译错误,list::iterator没有重载<或>
    cout<<*(v.begin()+1)<<endl;
    //cout<<*(l.begin()+1)<<endl; 			//编译错误,list::iterator没有重载+
    vector<int>::iterator itv=v.begin();
    list<int>::iterator itl=l.begin();
    itv = itv+2;
    //itl=itl+2; 												//编译错误,list::iterator没有重载+
    itl++; 															//list::iterator中重载了++，只能使用++进行迭代访问。
    itl++;
    cout<<*itv<<endl;
    cout<<*itl<<endl;
    getchar();
    return 0;
}
```

vector拥有一段连续的内存空间，能很好的支持随机存取，
因此`vector<int>::iterator`支持“+”，“+=”，“<”等操作符。

list的内存空间可以是不连续，它不支持随机访问，
因此`list<int>::iterator`则不支持“+”、“+=”、“<”等

`vector<int>::iterator`和`list<int>::iterator`都重载了“++”运算符。

### 3. 总结

如果需要<u>高效的随机存取</u>，而不在乎插入和删除的效率，使用vector;
如果需要<u>大量的插入和删除</u>，而不关心随机存取，则应使用list。