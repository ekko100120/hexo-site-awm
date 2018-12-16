---
layout: post
title: mockito 入门用法总结
date: 2018-11-04 20:41:27
tags: ['java', 'test', 'mock', 'mockito']
category: ['后台', 'java']
---

# mockito 用法总结

Mock测试是单元测试中的重要方法之一，它解决了在单元测试过程中一些棘手的问题，比如当我们的测试代码依赖太多的外部的因素（如其他类的API），这些外部的因素是不稳定的，我们不希望我们的测试代码因为这些外部因素而失败，还有就是有些依赖不好去构造（如`HttpServletRequest`），所以，我们急需一个能够模拟其他依赖行为的工具，`mockito` 是针对java语言的单元测试Mock工具，本文主要对`mockito`的一些常用用法进行总结。

[demo 地址](https://github.com/bulldogcfq/learn-mockito)

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

  - 当mock对象的某个行为（方法）没有被stubbed，该行为会智能的返回一个值，如返回值类型是`String`时返回`""`，返回值是`Integer/int`时返回0，返回值是`Boolean/boolean`时返回false，普通对象则返回`null`。
  - stubbing可以被覆盖，当一个对象的行为（方法）被stubbing两次时，前面的stubbing会被后面的stubbing覆盖
  - 一旦对象的方法被stubbing，无论该方法被stubbing多少次，都会返回stubbing的值

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
    when(mockedList.get(intThat(new ArgumentMatcher<Integer>() {
      @Override
      public boolean matches(Integer integer) {
        return integer < 5;
      }
    }))).thenReturn("element less than 5");

    when(mockedList.get(intThat(integer -> integer > 5))).thenReturn("element greater than 5");

    //following prints "element"
    System.out.println(mockedList.get(0));
    System.out.println(mockedList.get(5));
    System.out.println(mockedList.get(999));

    //you can also verify using an argument matcher
    verify(mockedList).get(eq(999));
  }
```

上面的方法规定传入任何int类型的参数，都会返回element，`mockito`内置了一些参数匹配器，在`ArgumentMatchers`类中，如果逻辑比较复杂，我们还可以自己实现ArgumentMatcher接口的matches方法，并配合XThat使用

Note:  

- 参数匹配器默认返回一个合适的值，如`Integer/int`返回0，所以它只可以适用在stubbing和verify中
- 如果使用参数匹配器，则所有的参数必须都适用匹配器

## 断言方法调用次数

`mockito`提供了`times()/atLeast()/never()`方法断言mock对象方法调用的次数，默认为times(1)，即只调用一次

```java
@Test
  public void verifyExactNumberOfInvocations() {
    List mockedList = mock(List.class);
    //times(1) is the default. Therefore using times(1) explicitly can be omitted.
    //using mock
    mockedList.add("once");

    mockedList.add("twice");
    mockedList.add("twice");

    mockedList.add("three times");
    mockedList.add("three times");
    mockedList.add("three times");

    //following two verifications work exactly the same - times(1) is used by default
    verify(mockedList).add("once");
    verify(mockedList, times(1)).add("once");

    //exact number of invocations verification
    verify(mockedList, times(2)).add("twice");
    verify(mockedList, times(3)).add("three times");

    //verification using never(). never() is an alias to times(0)
    verify(mockedList, never()).add("never happened");

    //verification using atLeast()/atMost()
    verify(mockedList, atLeastOnce()).add("three times");
    verify(mockedList, atLeast(2)).add("three times");
    verify(mockedList, atMost(5)).add("three times");
  }
```

`mockito`还提供了`verifyZeroInteractions`,`verifyNoMoreInteractions`方法断言对象方法从未被调用和不会再被调用

```java
//using mocks - only mockOne is interacted
 mockOne.add("one");

 //ordinary verification
 verify(mockOne).add("one");

 //verify that method was never called on a mock
 verify(mockOne, never()).add("two");

 //verify that other mocks were not interacted
 verifyZeroInteractions(mockTwo, mockThree);

  //using mocks
 mockedList.add("one");
 mockedList.add("two");

 verify(mockedList).add("one");

 //following verification will fail
 verifyNoMoreInteractions(mockedList);
```

## 断言mock对象行为调用的顺序

- 断言单个对象
    ```java
    @Test
    public void verification_in_order() {

      // A. Single mock whose methods must be invoked in a particular order
      List singleMock = mock(List.class);

      //using a single mock
      singleMock.add("was added first");
      singleMock.add("was added second");

      //create an inOrder verifier for a single mock
      InOrder inOrder = inOrder(singleMock);

      //following will make sure that add is first called with "was added first, then with "was added second"
      inOrder.verify(singleMock).add("was added first");
      inOrder.verify(singleMock).add("was added second");
    }
    ```
- 断言多个对象
    ```java
      @Test
      public void verification_in_order() {
        // B. Multiple mocks that must be used in a particular order
        List firstMock = mock(List.class);
        List secondMock = mock(List.class);

        //using mocks
        firstMock.add("was called first");
        secondMock.add("was called second");

        //create inOrder object passing any mocks that need to be verified in order
        InOrder inOrder2 = inOrder(firstMock, secondMock);

        //following will make sure that firstMock was called before secondMock
        inOrder2.verify(firstMock).add("was called first");
        inOrder2.verify(secondMock).add("was called second");
      }
    ```

断言mock对象方法调用顺序不要求我们对调用的每一个方法进行断言，我们可以只断言我们关心的方法，只要他们的执行顺序是正确的

## 链式调用

stubbing 支持链式调用

```java
  @Test
  public void stubbing_consecutive_calls() {
    List mockedList = mock(List.class);

    when(mockedList.get(0))
            .thenThrow(new RuntimeException())
            .thenReturn("foo");

    try {
      //First call: throws runtime exception:
      mockedList.get(0);
    } catch (Exception e) {
      e.printStackTrace();
    }

    //Second call: prints "foo"
    System.out.println(mockedList.get(0));

    //Any consecutive call: prints "foo" as well (last stubbing wins).
    System.out.println(mockedList.get(0));

    //shorter version of consecutive stubbing
    //this override the previous stubbing, even last value not called
    when(mockedList.get(0)).thenReturn("one", "two", "three");
    System.out.println(mockedList.get(0));
    System.out.println(mockedList.get(0));
    System.out.println(mockedList.get(0));
  }
```

链式调用的语法是when().thenX().thenX()...，上面的方法的第一段代码规定第一次调用抛出一个运行时异常，第二次调用返回'foo'，链式调用还有一种更简洁的写法，适合多个相同的thenX()进行链式调用，具体的语法为thenX(arg1, arg1, arg3)，等价于thenX(args).thenX(arg2).thenX(arg3)。

要注意的是，多次stubbing不会依次被调用，而是会覆盖之前的stubbing

## 使用Answer接口

一般来说`thenReturn`和`thenThrow`可以满足大多数需求，但有些逻辑比较的复杂的时候，我们可以使用`thenAnswer()`方法

```java
  @Test
  public void stubbing_with_callbacks() {
    List mockedList = mock(List.class);

    when(mockedList.get(anyInt())).thenAnswer(
            new Answer() {
              public Object answer(InvocationOnMock invocation) {
                Object[] args = invocation.getArguments();
                Object mock = invocation.getMock();
                return "called with arguments: " + Arrays.toString(args);
              }
            });

    //Following prints "called with arguments: [foo]"
    System.out.println(mockedList.get(999));
    System.out.println(mockedList.get(999));
  }
```

## 使用spy对象

使用spy对象会去调用真实的方法，除非该方法被stubbing

```java
  @Test
  public void spy_on_real_objects() {
    List list = new LinkedList();
    List spy = spy(list);

    //optionally, you can stub out some methods:
    when(spy.size()).thenReturn(100);

    //using the spy calls *real* methods
    spy.add("one");
    spy.add("two");

    //prints "one" - the first element of a list
    System.out.println(spy.get(0));

    //size() method was stubbed - 100 is printed
    System.out.println(spy.size());

    //optionally, you can verify
    verify(spy).add("one");
    verify(spy).add("two");

    //Impossible: real method is called use doReturn().when()
    try {
      when(spy.get(3)).thenReturn(1);
      System.out.println(spy.get(0));
    } catch (Exception e) {
      e.printStackTrace();
    }

    //You have to use doReturn() for stubbing to spy
    doReturn(0).when(spy).get(anyInt());
    try {
      System.out.println(spy.get(3));
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
```

Note:
当spy对象与when()方法结合使用时，要小心，因为spy对象会去调用真实的对象方法，使用when()  API stub 就会去调用真实的方法，所以stubbing spy 对象的时候，建议使用doX().when() API

## 参数捕获

使用`ArgumentCaptor`可以捕获调用参数，从而对参数进行断言，`ArgumentCaptor`能够让我们对mock对象的行为和参数分开进行断言

```java
  @Test
  public void capturing_arguments_for_further_assertions() {
    class Person {
      private String name;
      private int age;

      public String getName() {
        return name;
      }

      public void setName(String name) {
        this.name = name;
      }

      public int getAge() {
        return age;
      }

      public void setAge(int age) {
        this.age = age;
      }
    }
    Person mockedPerson = mock(Person.class);

    mockedPerson.setName("chenfuqiang");
    ArgumentCaptor<String> argument = ArgumentCaptor.forClass(String.class);
    verify(mockedPerson).setName(argument.capture());
    assertEquals("chenfuqiang", argument.getValue());
  }
```

一般来说，参数匹配器已经能够满足断言的需求，除非有以下几种情况

- 参数断言复用较多
- 需要对参数进行更深层次的断言

## 重置mock对象

使用reset方法可以重置mock对象之前stubbing，就好像它是刚刚mock出来的一样，这和spring结合使用很方便，我们可以在每个测试用例前将所有的mock对象重置一下，避免测试用例间会互相干扰

```java
  @Test
  public void resetting_mocks() {
    List mockedList = mock(List.class);
    when(mockedList.size()).thenReturn(100);
    assertEquals(100, mockedList.size());
    reset(mockedList);
    //will print 0
    System.out.println(mockedList.size());
  }
```

## 使用BDD测试风格

BDD(Behavior Driven Development )测试风格使用//givin//when//then三步走，`mockito`默认的API与之不能很好的吻合，`mockito`提供了另一套适合BDD测试风格的API，用于BDD测试

```java
import static org.mockito.BDDMockito.*;

 Seller seller = mock(Seller.class);
 Shop shop = new Shop(seller);

 public void shouldBuyBread() throws Exception {
   //given
   given(seller.askForBread()).willReturn(new Bread());

   //when
   Goods goods = shop.buyBread();

   //then
   assertThat(goods, containBread());
 }
```

## 使用注解

`mockito`提供了一套注解`@Mock`，`@Spy`，`@InjectMocks`，`@Captor`用于方便开发，这些注解需要显式调用` MockitoAnnotations.initMocks(testClass)`或是使用`MockitoJunitRunner`启用

- `@Mock`，创建mock对象

```java
public class Test{
  @Mock
  List mockedList;

  /**
   * enable @mock annotation
   * Note: can use MockitoJunitRunner instead
   */
  @Before
  public void setup() {
    MockitoAnnotations.initMocks(this);
  }

  @Test
  public void shorthand_for_mocks_creation_use_mock_annotation() {
    mockedList.add("one");

    verify(mockedList).add("one");
  }
}
```

- `@Spy` 创建Spy对象

```java
@RunWith(MockitoJunitRunner.class)
public class Test {
    @Spy
  private ArrayList<String> spyList;

  @Test
  public void use_spy_annotation() {
    spyList.add("one");
    System.out.println(spyList.size());
  }
}
```

- `@Captor` 创建参数捕获对象
  
```java
class Person{
  private String name;

  public Person(String name) {
    this.name = name;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }
}

public class Test {
    @Captor
  private ArgumentCaptor<Person> personArgumentCaptor;

  @Test
  public void use_captor_annotation() {
    List<Person> mockedList = (List<Person>)mock(List.class);

    Person person = new Person("hello");

    when(mockedList.add(person)).thenReturn(true);
    
    mockedList.add(person);
    
    verify(mockedList).add(personArgumentCaptor.capture());
    
    assertEquals(person.getName(), personArgumentCaptor.getValue().getName());

  }
}
```

- `@InjectMocks` 自动注入mock对象依赖，自动注入规则和Spring相似，支持构造器注入，setter方法注入和属性注入，下面展示属性注入，其他类似

```java
class DependentService{
    ...
}

class TestService {
  private DependentService dependentService;
}

class TestClass {
  @Mock
  private DependentService dependentService;

  @InjectMocks
  private TestService testService;
}
```

上述代码将DependenciedService对应的mock对象自动注入到testService中

Note(11-22补充)
在 `@Mock` 和 `@InjectMocks` 配合使用的时候，只能使用一种注入方式，不能混合使用

```java
@Service
public class TestService {
  @AutoWired
  private TestServiceInjected1 service1;
  private TestServiceInjected2 service2;
  public TestService(TestServiceInjected1 service1) {
    this.service1  = service1;
  }
}

@RunWith(MockitoJUnitRunner.class)
public class TestServiceTest {
  @Mock
  private TestServiceInjected1 service1;
  @Mock
  private TestServiceInjected2 service2;
  @InjectMocks
  private TestService;
}

```
上面这段代码，`TestSercie` 在 Spring 框架中可以工作的很好，所有的依赖都能智能地注入进来，但是测试代码就没那么幸运了， 测试代码中的 `TestService` 中的 `service2` 会是一个 `null`， 在测试的过程中可能会报空指针错误，我猜可能是 `Mockito` 只能使用一种注入方式，但包含构造有参构造方法时，默认使用的构造方法注入，所以只把 `servie1` 注入进来。但是如果你提供了多个构造方法，`Mockito`会智能地帮你注入依赖， 参考下面这个。

```java
@Service
public class TestService {
  @AutoWired
  private TestServiceInjected1 service1;
  private TestServiceInjected2 service2;
  public TestService(TestServiceInjected2 service2) {
    this.service2  = service2;
  }
  @AutoWired
  public TestService(TestServiceInjected2 service2， TestServiceInjected2 service2) {
    this.service1 = service1;
    this.service2  = service2;
  }
}
```
上面这段代码 在 Spring 和 `Mockito` 中都能很好的工作，但是正常情况下，我们都不会这么写对不对，那就不要这么写，统一使用一种注入方式就好了鸭。

## 参考文档

- [mockito文档](https://static.javadoc.io/org.mockito/mockito-core/2.23.0/org/mockito/Mockito.html#never_verification)
- [mockito 简明教程](https://www.aliyun.com/jiaocheng/549032.html)
- [mockito 教程](https://www.cnblogs.com/Ming8006/p/6297333.html)
- [Mock InjectMocks ( @Mock 和 @InjectMocks )区别](https://blog.csdn.net/kurt17/article/details/52670482)
