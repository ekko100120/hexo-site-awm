---
layout: post
title: powermock-使用Whitebox打破java类封装
date: 2018-11-01 20:05:48
tags: ['java', 'test', 'powermock']
category: ['后台', 'java']
---

# powermock-使用Whitebox打破java类封装

> 在Java的单元测试中, 由于Java的封装性，一些私有的变量和方法是不易测试的，因为我们特么根本就拿不到，不过不要紧，Whitebox类提供了一系列的方法让你去打破Java类的封装，使用Whitebox后，任何一个类在你面前都是裸奔状态。

## API

不算上方法重载，Whitebox一共提供了4中方式

```java
    //设置私有变量
    Whitebox.setInternalState(..);

    //获取私有变量
    Whitebox.getInternalState(..);

    //调用私有方法
    Whitebox.invokeMethod(..);

    //调用私有构造函数
    Whitebox.invokeContructor(..);
```

## 获取私有变量