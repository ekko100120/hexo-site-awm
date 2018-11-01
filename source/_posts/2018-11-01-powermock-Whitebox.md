---
layout: post
title: powermock-使用Whitebox打破java类封装
date: 2018-11-01 20:05:48
tags: ['java', 'test', 'powermock']
category: ['后台', 'java']
---

# PowerMock-使用 Whitebox 打破 java 类封装（1）

> 在Java的单元测试中, 由于Java的封装性，一些私有的变量和方法是不易测试的，因为我们特么根本就拿不到，不过不要紧，  `org.powermock.reflect.Whitebox` 类提供了一系列的方法让你去打破Java类的封装，使用`Whitebox`后，任何一个类在你面前都是裸奔状态。

## API

不算上方法重载，Whitebox一共提供了4个API

```java
    //设置私有变量
    Whitebox.setInternalState(..);

    //获取私有变量
    Whitebox.getInternalState(..);

    //调用私有方法
    Whitebox.invokeMethod(..);

    //调用私有构造函数
    Whitebox.invokeConstructor(..);
```

## getInternalState

假如我们如下一个类

```java
    public class ServiceHolder {

        private final Set<Object> services = new HashSet<Object>();

        public void addService(Object service) {
            services.add(service);
        }

        public void removeService(Object service) {
            services.remove(service);
        }
    }
```

这个类中的services变量时私有的，这个类中的一些API修改了这个私有变量的状态，在测试这个类时，我们必须知道这个私有变量的状态，一个简明的做法是为这个私有变量提供一个`package-private`或者是`protected`的`getter`方法，但是这么做有两个不好的地方，1.是破坏了java类的封装性，2.增加了只在测试时才能用到的代码。使用Whitebox可以很好的解决这样的问题，我们来看看具体要怎么做。

```java
    @Test
    public void should_services_added_when_addServices_method_invoked() {
        ServiceHolder tested = new ServiceHolder();
        final Object service = new Object();
        tested.addService(service);
        // This is how you get the private services set using PowerMock
        Set services = Whitebox.getInternalState(tested, "services");
        assertEquals("Size of the \"services\" Set should be 1", 1, services.size());
        assertSame("The services Set should didn't contain the expect service", service, services.iterator().next());
    }
```

上面的代码中`getInternalState`一共传入两个参数，第一个是java类实例，第二个是实例属性名

如果PowerMock的版本在1.0以上，且要获取的私有变量的类型是唯一的，你同样可以使用传入属性的类型来获取，

```java
    Set<String> services = Whitebox.getInternalState(tested, Set.class);
```

如果类之间存在继承关系，子类覆盖了父类中的私有变量

```java
    //父类
    public class ParentService {
        private String name;
        public void setName(String name) {
            this.name = name;
        }
    }
    //子类
    public class SubService extends ParentService {
        private String name;
        public void setName(String name) {
            this.name = name;
            super.setName(name + "'s parent");
        }
    }
```

在测试中如果要获取父类的私有变量，可以添加父类的类类型作为第三个参数

```java
  @Test
  public void should_set_parent_name_when_set_self_name () {
      SubService subService = new SubService();
      subService.setName("sub");
      String name = Whitebox.getInternalState(subService, "name", ParentService.class);
      assertEquals("parentService's name field should be sub's parent", "sub's parent", name);
  }
```

如果类属性类型唯一，可以使用类属性的类类型取代类属性名

```java
    //Use class type instead of field name
    String name = Whitebox.getInternalState(subService, String.class, ParentService.class);
```

## setInternalState

设置一个私有变量同样很简单，假如我们有如下类

```java
    public class ReportGenerator {

        @Injectable
        private ReportTemplateService reportTemplateService;

        public Report generateReport(String reportId) {
            String templateId = reportTemplateService.getTemplateId(reportId);
            /*
            * Imagine some other code here that generates the report based on the
            * template id.
            */
            return new Report("name");
        }
    }
```

上述类中使用依赖注入，如果你使用的是Spring框架，则是使用`@Autowired`标签，使用依赖注入可以很方便的管理类之间的依赖关系，但是写这个类的单元测试的话就不好写了，因为这个属性是私有的，脱离了Spring(可以使用`SpringJunit4ClassRunner`启动spring容器解决依赖注入的问题)，我们无法将这个属性的实例注入到这个类中，一种解决方案是提供这个属性的`setter`方法，这样同样会导致`setter`方法只为测试而写的尴尬，Whitebox类提供了`setInternalState`方法解决了这样的尴尬

```java
    Whitebox.setInternalState(tested, "reportTemplateService", reportTemplateServiceMock);
```

Whitebox.setInternalState接受三个参数，第一个是类实例，第二个是类属性名，第三个是类属性值，这样，在测试时我们可以向类中注入自己mock的对象
如果你使用的是PowerMock 1.0以上的版本，且该类型属性的类型在类中是唯一的, 则第二个参数可以省略，就像下面这样

```java
    Whitebox.setInternalState(tested, reportTemplateServiceMock);
```

如果类之间存在继承关系，而我们又要设置父类的属性, 看下面这个例子

```java
    //父类
    public class ParentService {
        private String name;
        public String getName() {
            return this.name;
        }
    }
    //子类
    public class SubService extends ParentService {
        private String name;
        public String getName() {
            return super.getName() + "'s " + this.name;
        }
    }
```

子类的`getName`方法依赖父类的`getName`方法，我们可以做如下测试

```java
    @Test
    public void should_concat_parent_name_when_get_self_name() {
        SubService subService = new SubService();
        //Whitebox.setInternalState(subService, "name", "parentService", ParentService.class);
        //Whitebox.setInternalState(subService, "name", "subService");
        //Use class type instead of field name
        Whitebox.setInternalState(subService, String.class, "parentService", ParentService.class);
        Whitebox.setInternalState(subService, String.class, "subService");
        String name = subService.getName();
        assertEquals("name should concat parent'name and be parentService's subService",
            "parentService's subService", name);
    }
```