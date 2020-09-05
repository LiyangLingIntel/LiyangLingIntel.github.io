---
layout:     post
title:      Chapter1.2 遵循PEP8风格规范
subtitle:  【译】Effective Python 第二版
date:       2020-09-03
author:     Leon Ling
header-style: text
catalog: true
tags:
    - Python
    - Tutorial
    - EffetivePython
---

Python改进建议#8 (Python Enhancement Proposal #8)，也是通常大家熟知的PEP8，是指导规范化python代码的风格指南。原则上只要符合语法，你可以写任意形式的python代码。但是使用具有一致性的风格可以让你的python代码更加易读，也可以促进你在社区或者企业中和其他python开发人员协作的效率。即使你是唯一要读你写的代码的人，使用这套代码风格也可以帮助你在日后做出修改时更加方便，也可以表面许多常见的错误。

PEP8 提供了大量的关于如何编写清晰的python代码的细节，它也随着python的改进一直更新。如果有兴趣，非常有价值去[PEP官网](https://www.python.org/dev/peps/pep-0008/)阅读PEP8完整的风格指南。这里有一些规则你应该确保遵守。

### 空格

在python中，空格在语法上是非常重要的。Python程序员尤其需要对空格在代码清晰度上的影响保持敏感。
请遵守这些空格相关的准则：

* 使用空格而不是`tab`进行缩进
* 使用四个空格来表示语法上每一级的缩进
* 每行代码的长度不超过79个字符
* 连续的长表达式
* 在同个文件里，函数和class应该被两个空行分开
* 在同个class里，方法应该被一个空行分开
* 在一个字典里，key和冒号之间不能有空格，如果对应的value和key在同一行的话，在value前加一个空格
* 在`=`前后应该且只能分别放一个空格
* 对于类型标注，确保变量名和冒号之间没有间隔，在类型信息前加一个空格    E.g. `var: str`

#### 命名

PEP8 建议对语言中的不同部分用独特的命名风格。这样的约定可以让读代码时区分每个名字对应的类型更加容易。
请遵守这些命名相关的准则：

* 函数，变量和属性应该使用如 `lowercase_underscore` 小写加下划线的格式
* Protected 实例属性应该使用如 `_leading_underscore` 单下划线开头的格式
* Private 实例属性应该使用如 `__double_leading_undersocre` 双下划线开头的格式
* Classes(包括异常exceptions)应该使用如 `CapicalizedWord` 大驼峰形式的格式
* 模块级别的常量应该使用如 `ALL_CAPS` 全大写的格式
* Class的实例方法应该使用 `self` 作为第一个参数名，其作用是指向自己的对象
* Class的类方法应该使用 `cls` 作为第一个参数名，其作用是指向自己的class

#### 表达式和声明

[《Pythonの禅(The Zen of Python)》](https://www.python.org/dev/peps/pep-0020/)中提到：“一件事应该被一种——最好只有一种——明确的方式来解决。” 
PEP8 尝试在他的准则中针对表达式和声明加入这种风格：

* 使用内联否定 (`if a is not b`) 而不是内联肯定的否定的表达式 (`if not a is b`)。
* 不要使用和0比较长度的方式来判断容器或者句子 (如`[]` 或者` ''`) 是否为空，如 `if len(somelist) == 0`。假定空值会被隐式等价为`False`，使用 `if not somelist` 来判断。
* 对待飞空的容器和句子 (如`[]` 或者` ''`) 也是类似的。用 `if somelist` 隐式地将非空变量表达为 `True`。
* 避免使用一行的 `if`语句，`for` 和 `while` 循环，以及 `except` 复合语句。将他们扩展多行来保证表述清晰。（小声bb：虽然写起来确实很爽）
* 如果你不能在一行内写完一条表达式，用括号将表达式包裹起来，并通过换行符和缩进来确保清晰易读。
* 尽可能用括号，换行符和缩进来编写占据多行的表达式，而不是使用行连续符 `\`。

#### 模块的引用

PEP8 在怎么引入模块和使用它们编码上给出了一些建议：

* `import`语句（包括 `from x import y` ）总是被放在文件最开头。
* 总是使用模块的绝对名称来引用他们，而不是相对于模块当前路径的名字。比如，为了引用包 `bar` 下的 `foo` 模块（这里`bar` 和 `foo`指用户自定义模块），你应该使用 `from bar import foo`，而不仅仅是  `import foo`。
* 如果你必须做相对引用，使用显式的语法：`from . import foo`。
* 模块引用应该按照下面的顺序分为几块：标准库模块，第三方模块，你自己的模块。每个部分内部都应该按照字母顺序排序。

**小提示：**

[Pylint 工具](https://www.pylint.org) 是一款非常受欢迎的python源代码静态分析器。Pylint提供了遵从PEP8风格的自动纠错和检测python程序中其他类型的常见错误。许多IDE和编辑器也都集成自动格式化工具或者支持类似的插件。

#### 需要记住的

* 在编写Python代码的时候坚持遵循PEP8风格规范。
* 在大型Python社区中和大家共用一套通用风格会提高与其他人的协作效率。
* 使用具有一致性的风格会使你在日后修改代码的时候更加轻松。