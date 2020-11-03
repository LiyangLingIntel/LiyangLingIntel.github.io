# LLVM学习笔记 - Pass

[toc]

LLVM 的Pass框架是LLVM系统的一个很重要的部分。每个Pass都是做优化或者转变的工作，LLVM的优化和转换工作就是由很多个Pass来一起完成的。

Pass就是LLVM系统转化和优化的工作的一个节点，每个节点做一些工作，这些工作加起来就构成了LLVM整个系统的优化和转化。Pass架构这么做的话，可重用性非常好，你可以选择已有的一些Pass，自己去构建出自己想要的优化和转化效果。并且自己也可以重新写Pass去做自己想要的优化和转变，因为每个Pass都可以独立存在，所以新建Pass并不用考虑LLVM之前的优化和转化是怎么做的，自己可以只运行自己新建的Pass，这样可以方便的实现自己想要的效果。

### 2. Pass 源码简析

#### 2.1 Pass.h

**Position:** [llvm/include/llvm/Pass.h]()

此头文件主要对和Pass相关的几个类进行了声明。
主要有Pass、ModulePass、ImmutablePass、FunctionPass、BasicBlockPass这几个类，也正是LLVM的Pass框架所最常用的几个类。在Pass的编写或者修改的工作中，基本上都是在和这几个类打交道，所以对于这几个类要想有一个深的了解，这个头文件是必须读的。
ModulePass、ImmutablePass、FunctionPass、BasicBlockPass这几个类都是Pass的子类，也是在写Pass的时候最容易直接用到的类。自己编写LLVM的Pass的时候，使用的最多是FunctionPass和ModulePass这两个。而这两个之中，FunctionPass要使用的比ModulePass更为多一点，因为如果不删除函数，一般都是处理函数内部的信息，那么使用FunctionPass就可以了。还有一种情况是需要使用ModulePass的，那就是在需要处理MetaData的时候，这种情况之下也会用到ModulePass的，因为Metadata是在函数之外的。

#### 2.2 Pass.cpp

**Position:** [llvm/lib/IR/Pass.cpp]()

此文件主要是对Pass.h中声明的几个类的实现。除此之外，还有对RegisterAGBase、PassRegistrationListener、AnalysisUsage这三个类的实现。

#### 2.3 PassSupport.h

**Position:** [llvm/include/llvm/PassSupport.h]()

此头文件被包含在Pass.h这个头文件的尾部，所以如果文件已经包含了Pass.h，其实就也可以访问PassSupport.h。
这个文件中主要是PassInfo、RegisterPass、RegisterAGBase、RegisterAnalysisGroup、PassRegistrationListener这几个类的声明。

PassInfo类的每一个对象都对应着一个实际存在的Pass，并且保存着这个Pass的信息。

RegisterPass这个类是一个模板类，这个模板类的类型就是Pass的名字。它是PassInfo的子类，主要用来注册Pass。完成注册之后，在PassManager管理的内部数据库里才能找到这个Pass。需要注意的是，这个模板类的使用必须是在全局范围之内的。可以从最简单的Pass例子--[Hello]() 中去看这个模板类的使用方法。

RegisterAGBase是RegisterAnalysisGroup类的基类，而RegisterAGBase类又是PassInfo类的子类。其中RegisterAGBase类名字中的AG就是AnalysisGroup的缩写，这种命名方式在LLVM的源码中被大量的应用，比如MetaData在一些类的名字里就被缩写为MD

RegisterAnalysisGroup这个类的作用主要是将一个Pass注册成为一个分析组的成员，当然在进行此操作之前，这个Pass必须被首先注册Pass成功。一个Pass可以被注册到多个分析组中。同一个Pass在多个分析组中，依然是根据这个Pass的名字进行标识的。

PassRegistrationListener这个类主要负责在运行时时候Pass的注册与否，并且会在Pass被load和remove的时候，去调用回调函数。

#### 2.5 PassAnalysisSupport.h

**Position:** [llvm/include/llvm/PassSupport.h]()

此头文件被包含在Pass.h这个头文件的尾部，所以如果文件已经包含了Pass.h，其实就也可以访问PassAnalysisSupport.h。
这个头文件之中，主要是AnalysisUsage和AlysisResolver两个类的声明以及Pass类的部分函数的实现。
其中，AnalysisUsage主要负责表示分析出来的使用信息。
AnalysisResolver是一个Pass对象用来从Pass Manager获取所有的分析信息的简单接口。