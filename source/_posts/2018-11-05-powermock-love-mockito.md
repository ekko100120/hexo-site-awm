---
layout: post
title: PowerMock Love Mockito
date: 2018-11-07 15:06:21
tags: ['java', 'test', 'mock', 'mockito']
category: ['后台', 'java']
---

# PowerMock Love Mockito

`PowerMock`是一个 java 单元测试框架，他可以用来解决单元测试过程中一些棘手的问题，如如何 mock 静态方法和私有方法，PowerMock 需要结合其他 Mock 框架( Mockito 或 EasyMock )使用，由于我使用的是 `mockito` ，所以这篇文章主要介绍 `PowerMock` 和 `Mockito` 结合的一些主要用法。

[demo 地址](https://github.com/bulldogcfq/learn-powermock)

## 动机

我们已经有了 `Mockito` ，为什么还要使用 `PowerMock` 呢？其实，`PowerMock` 可以看作是对 `Mockito` 的一个增强，`Mockito` 做的事 `PowerMock` 并没有再实现一遍，而是依赖它的实现，在它的基础上，增加了 mock 静态类，stubbing 私有方法和构造方法以及断言静态方法，私有方法和构造方法。

## 安装和使用

maven 依赖

```xml
  <dependencies>
      <dependency>
          <groupId>org.powermock</groupId>
          <artifactId>powermock-module-junit4</artifactId>
          <version>1.7.1</version>
          <scope>test</scope>
      </dependency>
      <dependency>
          <groupId>org.powermock</groupId>
          <artifactId>powermock-api-mockito2</artifactId>
          <version>1.7.1</version>
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
这里有个坑，最新版(2.23.0)的 Mockito 和最新版(1.7.1)的 powermock 不兼容，mock 静态类的时候会报错，在 PowerMock 2.x 版本已经修了这个问题，由于 2.x 还没出 release 版本，所以我本地就显式引入 2.8.9 版本。

测试类

```java
@RunWith(PowerMockRunner.class)
@PrepareForTest({StaticClass.class, ClassWithPrivateMethod.class})
public class TestClass {
  .....
}
```

添加 `@RunWith(PowerMockRunner.class)` 注解，使我们的代码可以使用 `PowerMockito` 的特性，如果我们要 mock 静态类，又或是要 stubbing 和 verify 某个类的私有方法和构造方法，我们需要使用 `@PrepareForTest({StaticClass.class, ClassWithPrivateMethod.class)` 注解，参数 value 为我们要 mock 静态类或是要 stubbing 和 verify 私有方法和构造方法的类的类类型。

## mock 含有静态方法的类

```java
import static org.powermock.api.mockito.PowerMockito.*;
import static org.junit.Assert.*;
...
@Test
public void mock_statics_object_and_verify_static_method() {

    mockStatic(StaticClass.class);

    when(StaticClass.getName()).thenReturn("123");
    String name = StaticClass.getName();

    verifyStatic(StaticClass.class);
    StaticClass.getName();

    assertEquals("123", name);
}
```

`mockStatic` 可以 mock 静态类，**无返回值**，静态类直接拿来用就好了，`verifyStatic` 用于断言静态类的行为，**无返回值**，紧接着需要调用静态类的某个方法，声明我要断言哪个方法，这和 Mockito 的用法有点区别。

## 参数匹配

```java
@Test
public void argument_matcher() {
  PowerMockito.mockStatic(StaticClass.class);

  when(StaticClass.setName(anyString())).thenReturn("123321");

  String name = StaticClass.setName("123");

  PowerMockito.verifyStatic(StaticClass.class);
  StaticClass.setName(anyString());

  assertEquals("123321", name);
}
```

参数匹配使用 Mockito 包内的参数匹配器就好了，用法没什么不同。

## 断言静态方法调用次数

```java
@Test
public void verify_exact_number_of_calls() {
  PowerMockito.mockStatic(StaticClass.class);

  when(StaticClass.setName(anyString())).thenReturn("123321");

  String name = StaticClass.setName("123");
  StaticClass.setName("123");

  PowerMockito.verifyStatic(StaticClass.class, times(2));
  StaticClass.setName(anyString());

  assertEquals("123321", name);
}
```

静态方法次数断言给 `verifyStatic` 第二个参数传入匹配器 `times` 参数就好了，这个参数匹配器同样来自 `Mockito` 包。

## 使用 doX 语法 stubbing 含有静态方法的类

```java
@Test
public void stub_static_method_to_throw_exception() {
  PowerMockito.mockStatic(StaticClass.class);
  
  PowerMockito.doThrow(new RuntimeException("Mock error")).when(StaticClass.class);
  StaticClass.getName();

  //will throw RuntimeException
  try {
      System.out.println(StaticClass.getName());
  } catch (Exception e) {
      e.printStackTrace();
  }
}
```

使用 `when(Static.something()).thenX()` 的语法和 `Mockito` 的使用方法相同，但是，当使用 `doX().when()` 时稍有些不同，有点像前面的断言静态方法调用，`when` 方法传入静态类的类类型，紧接着马上调用静态方法。

## spy 含有静态方法的类

使用 `PowerMock` 的 spy 方法可以 spy 静态类，使用方式为 `PowerMockito.spy(StaticClass.class)`

```java
@Test
public void spy_statics_class() {
  spy(StaticClass.class);

  doReturn("spy statics class").when(StaticClass.class);
  StaticClass.getName();

  System.out.println(StaticClass.getName());

  verifyStatic(StaticClass.class);
  StaticClass.getName();
}
```

和 `mockito` 类似，`spy` 对象会去调用真实的方法，除非该方法被 stubbing ，上面这个方法 stubbing `StaticClass` 的静态方法返回一个自定义的值，然后断言这个方法被调用了一次，stubbing 和 verify 的用法和 `mock` 含有静态方法的类一样。

## spy 对象

`PowerMock` spy 对象的写法和 `Mockito` 没什么不同，但是用 `PowerMock` spy 的对象可以 stubbing 和 verify 私有方法和构造方法。下面马上就说到了

## stubbing 和 verify 私有方法

```java
@Test
public void verify_private_behavior() throws Exception {
  ClassWithPrivateMethod spy = spy(new ClassWithPrivateMethod());

  doReturn("123").when(spy, "privateMethod", "test");

  String test = spy.invokePrivateMethod("test");

  verifyPrivate(spy).invoke("privateMethod", "test");

  assertEquals("123", test);
}  
```

`PowerMockito` 的 `when(spyObject, methodName, args..)` 可以  stubbing 私有方法，第二个参数是方法名，接下来的是动态参数，即私有方法需要的参数。这个有个要注意的地方，这里需要使用 `doX.when()` 语法，因为spy对象会去调用真实的方法，使用 `when().thenX()` 语法会去真正的调用一次私有方法，这是我们不想看到的。`PowerMockito` 的 `verifyPrivate(spyObject).invoke(methodName, args..)` 可以断言私有方法的调用，`verifyPrivate` 还可以传入 `times(num)` 断言私有方法调用的次数。

## stubbing 和 verify 构造方法

```java
@Test
public void subbing_and_verify_constructor() throws Exception {
  ParentService mockedParentService = mock(ParentService.class);
  SubService mockedSubService = mock(SubService.class);

  whenNew(ParentService.class).withNoArguments().thenReturn(mockedParentService);
  whenNew(SubService.class).withArguments(anyString()).thenReturn(mockedSubService);

  ParentService parent = StaticClass.getService("parent");
  ParentService sub = StaticClass.getService("sub");

  assertEquals(mockedParentService, parent);
  assertEquals(mockedSubService, sub);

  verifyNew(SubService.class).withArguments(anyString());
  verifyNew(ParentService.class).withNoArguments();
}
```

`whenNew` 用于 stubbing 构造方法，可以接着调用 `withNoArguments` stubbing 无参构造函数，或是调用 `withArguments` stubbing 有参构造函数。`verifyNew(SomeClass.class)withX()` 可以断言构造函数是否被调用，还可以还可以传入 `times(num)` 断言构造函数调用的次数。

## tip

在 `PowerMock` 和 `Mockito` 混用的时候，因为这两个包提供了大致的API(比如 `when()`方法，当 stubbing 普通方法（至少不是静态方法）的时候，两个包中的 `when()` 方法都能使用，并无差别)，我们经常会疑惑到底该用哪个包里的，我的经验就是当使用 `PowerMock` 的时候，能用 `PowerMock` 里的API就用 `PowerMock` 里的，那你又要问了，哪些API `PowerMock` 没有提供，我大概总结一下就是所有的参数匹配器和 `verify()` 方法。
还有一个就是，当涉及到静态方法，私有方法以及构造方法的mock，stubbing，verify，一定要将所属类的类类型传入到 `@PrepareForTest()` 注解中。

## 参考文档

- [PowerMock 文档](https://github.com/powermock/powermock/wiki/Mockito)