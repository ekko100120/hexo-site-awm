---
layout: post
title: mockito 用法总结
date: 2018-11-04 20:41:27
tags: ['java', 'test', 'mock', 'mockito']
category: ['后台', 'java']
---

# mockito 用法总结

Mock测试是单元测试中的重要方法之一，它解决了在单元测试过程中一些棘手的问题，比如当我们的测试代码依赖太多的外部的因素（如其他类的API），这些外部的因素是不稳定的，我们不希望我们的测试代码因为这些外部因素而失败，还有就是有些依赖不好去构造（如`HttpServletRequest`），所以，我们急需一个能够模拟其他依赖行为的工具，`mockito` 是针对java语言的单元测试Mock工具，本文主要对`mockito`的一些常用用法进行总结。

## API 概览

`mockito`本身提供的API很少，所以它是简单易学的

- `mock/@mock` 用于创建mock对象
- `spy/@spy` 部分mock，与mock对象不同的是，spy对象会去调用真实的方法，除非该方法stubbed（意思是去规定对象行为的结果（如返回值，抛出异常等，下面就不对这个词进行解释了））
- `@injectMocks`自动注入mock对象
- `when()/givin()` 用于stubbing，定义对象的行为
- `verify()` 对mock对象的行为进行断言
- `Answer`对象行为结果统一接口，用于定义对象的行为返回结果，`mockito`内置了一些默认的实现，单逻辑复杂的时候，我们也可以自己实现这个接口
- `ArgumentMatcher`参数匹配器，对参数进行断言
  - `ArgumentCaptor` 参数匹配器的一种特殊实现

## 安装和引入依赖

```xml
  <!-- maven mockito 依赖 -->
  <dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>2.23.0</version>
    <scope>test</scope>
  </dependency>
```

```java
  //使用静态引入
  import static org.mockito.Mockito.*;
```

## 创建mock对象

`mock`方法用于创建mock对象

```java
  List mockedList = mock(List.class);
```

## 行为断言

`verify`方法可以对mock对象行为进行断言

```java
  @Test
  public void verifyBehavior() {
    // mock creation
    List mockedList = mock(List.class);

    // using mock object - it does not throw any "unexpected interaction" exception
    mockedList.add("one");
    mockedList.clear();

    // selective, explicit, highly readable verification
    verify(mockedList).add("one");
    verify(mockedList).clear();
  }
```

上面这个测试方法断言mock对象的`add`和`clear`会被调用，mock对象自身会保存所有方法调用的记录

## stubbing

`when(mockedObject.someMethod()).thenX()`可以定义mock对象行为的结果

```java
  @Test
  public void stubbing() {
    List mockedList = mock(List.class);

    //stubbing
    when(mockedList.get(0)).thenReturn("first");
    when(mockedList.get(1)).thenThrow(new RuntimeException());

    //following prints "first"
    System.out.println(mockedList.get(0));

    //following throws runtime exception
    try {
      System.out.println(mockedList.get(1));
    } catch (Exception e) {
      e.printStackTrace();
    }
    //following prints "null" because get(999) was not stubbed
    System.out.println(mockedList.get(999));

    //Although it is possible to verify a stubbed invocation, usually it's just redundant
    //If your code cares what get(0) returns, then something else breaks (often even before verify() gets executed).
    //If your code doesn't care what get(0) returns, then it should not be stubbed. Not convinced? See here.
    verify(mockedList).get(0);
    verify(mockedList).get(999);
  }
```

上面这个测试方法规定调用`get(0)`时返回`first`，调用`get(1)`时抛出一个运行时异常，相似的方法还有`thenAnswer`，`thenCallRealMethod`。

Note:

  - 当mock对象的某个行为（方法）没有被stubbed，该行为会智能的返回一个值，如返回值类型是`String`时返回`""`，返回值是`Integer/int`时返回0，返回值是`Boolean/boolean`时返回false，普通对象则返回`nyll`。
  - stubbing可以被覆盖，当一个对象的行为（方法）被stub两次时，前面的stub会被后面的stub覆盖
  - 一旦对象的方法被stubbed，无论该方法被stub多少次，都会返回stubbing的值

stubbing还有另一种使用形式`doX().when(mockedObject).someMethod()`，具体看下面代码

```java
  @Test
  public void do_return() {
      List mockedList = mock(List.class);
      doThrow(new RuntimeException()).when(mockedList).get(999);
      //will throw runtimeException
      mockedList.get(999);
  }
```

上面的代码规定调用`mockedList.get(999)`时抛出一个运行时异常

官方文档中推荐使用`when()`API，因为它是类型安全的，可读性也要好一点，但是存在即合理，有些情况下必须使用这个API

- stubbing一些无返回值的方法，因为when()不允许传入无返回值的方法
- stubbing spy对象的某些方法
- 要覆盖之前的stubbing

```java
  //stub spy object
  @Test
  public void some_test_method() {
    List spy = spy(new ArrayList());
    //Impossible: real method is called so spy.get(0) throws IndexOutOfBoundsException (the list is yet empty)    
    when(spy.get(0)).thenReturn(1);
    //You have to use doReturn() for stubbing
    doReturn("foo").when(spy).get(0);
  }

  //override previous stubbing
  @Test
  public void some_test_method() {
    List mockedList = mock;
    //Impossible: real method is called so spy.get(0) throws IndexOutOfBoundsException (the list is yet empty)    
    when(spy.get(0)).thenReturn(1);
    //You have to use doReturn() for stubbing
    doReturn("foo").when(spy).get(0);
  }
```

## 参数匹配器(ArgumentMatchers)

`mockito`默认适用`equals()`方法匹配我们的参数，但这有些不灵活，`mockito`的参数匹配器可以使参数匹配更加灵活

```java
  @Test
  public void argumentMatchers() {
    List mockedList = mock(List.class);

    //stubbing using built-in anyInt() argument matcher
    when(mockedList.get(anyInt())).thenReturn("element");

    //stubbing using custom matcher (let's say isValid() returns your own matcher implementation):
    when(mockedList.get(argThat(new HamcrestArgumentMatcher<>(new CustomMatcher<Integer>("less than 5") {
      @Override
      public boolean matches(Object o) {
        return Integer.parseInt(o.toString()) < 5 ;
      }
    })))).thenReturn("element");

    //following prints "element"
    mockedList.get(0);
    System.out.println(mockedList.get(999));

    //you can also verify using an argument matcher
    verify(mockedList).get(eq(999));
  }
```

上面的方法规定传入任何int类型的参数，都会返回element，如果逻辑比较复杂，我们还可以自己实现ArgumentMatcher接口的matches方法，并配合XThat使用

Note:  

- 参数匹配器默认返回一个合适的值，如`Integer/int`返回0，所以它只可以适用在stubbing和verify中
- 如果使用参数匹配器，则所有的参数必须都适用匹配器

## 断言方法调用次数

使用`times()`可以断言方法调用的次数