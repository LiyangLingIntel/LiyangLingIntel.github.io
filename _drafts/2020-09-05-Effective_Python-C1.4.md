---
layout:     post
title:      Chapter1.4 更偏向内嵌F-Strings而不是C风格的字符串格式化或者str.format
subtitle:  【译】Effective Python 第二版
date:       2020-09-05
author:     Leon Ling
header-style: text
catalog: true
tags:
    - Python
    - Tutorial
    - EffetivePython
---

> Chapter 1 Pythonic Thinking - Item 4: Prefer interpolated F-Strings over C-Style format strings and str.format

字符串存在于整个python代码库中。 它们用于在用户界面和命令行实用程序中呈现消息。也用于将数据写入文件和套接字。 也用于指定“异常”详细信息中出了什么问题（详见 [Chapter4.27 使用推导式而不是 map 或者 filter]()）。 还用于调试（详见 [Chapter9.27 考虑使用pdb进行交互式调试]() 和 [Chapter9.75 使用repr字符串调试输出]()）。