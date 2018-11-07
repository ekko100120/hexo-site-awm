---
layout: post
title: PowerMock Love Mockito
date: 2018-11-07 15:06:21
tags:  ['java', 'test', 'mock', 'mockito']
category: ['后台', 'java']
---

# PowerMock Love Mockito

`PowerMock`是一个java单元测试框架，他可以用来解决单元测试过程中一些棘手的问题，如如何mock静态方法和私有方法，PowerMock需要结合其他Mock框架(Mockito或EasyMock)使用，由于我使用的是`mockito`，所以这篇文章主要介绍 `PowerMock` 和 `Mockito` 结合的一些主要用法。

## 动机

我们已经有了 `Mockito` ，为什么还要使用 `PowerMock` 呢？其实，`PowerMock` 可以看作是对 `Mockito` 的一个增强，`Mockito` 做的事 `PowerMock` 并没有再实现一遍，而是依赖它的实现，在它的基础上，增加了 mock 静态类，stubbing 私有方法和构造方法以及断言静态方法，私有方法和构造方法。

## 安装和使用

maven依赖

```xml
    <dependencies>
        <dependency>
            <groupId>org.powermock</groupId>
            <artifactId>powermock-module-junit4</artifactId>
            <version>${powermock.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.powermock</groupId>
            <artifactId>powermock-api-mockito2</artifactId>
            <version>${powermock.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-core</artifactId>
            <version>2.8.9</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

Note:
    这里有个坑，最新版(2.23.0)的Mockito和最新版(1.7.1)的powermock不兼容，mock静态类的时候会报错，这个bug在PowerMock 2.x版本已经修复，由于2.x还没出 release 版本，所以我本地就显式引入2.8.9版本。


测试类

```java
@RunWith(PowerMockRunner.class)
@PrepareForTest({StaticClass.class, ClassWithPrivateMethod.class})
public class TestClass {
    .....
}
```






