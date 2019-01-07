---
layout: post
title: javascript 实现自定义继承的几种方式
tags:
  - javascript
  - extend
category:
  - javascript
date: 2019-01-07 10:29:41
---


# javascript 实现自定义继承

## 非构造函数继承

### object()方法

json格式的发明人Douglas Crockford 在2006年发表的一篇文章中提出一种实现继承的方法，主要思想是基于一个已有对象，返回一个以该对象为原型的对象。

```javascript
function object(proto) {
  function F() {}
  F.prototype = proto;
  return new F();
}
```

上面这个 `object` 方法使用一个空构造函数 `F` 作为桥接，让 `F.prototype` 指向第一个参数，这样使用关键字 `new` 调用 `F` 构造函数就会返回一个原型指向第一个参数的对象，从而实现继承。到了 `es5`，这个方法被规范化为 `Object.create`, 同时，`Object.create` 还可以设置第一个参数为null，返回一个不继承任何一个对象的对象，并且支持传入第二个参数，是要添加到新创建对象的可枚举属性（即其自身定义的属性，而不是其原型链上的枚举属性）对象的属性描述符以及相应的属性名称。这些属性对应`Object.defineProperties()`的第二个参数，所以我们可以这样写 `object` 方法。

```javascript
function object(proto) {
  if (typeof proto !== 'object') {
    throw new TypeError('proto is not a object');
  }
  if (Object.create) {
    return Object.create(proto);
  }
  if (proto === null) {
    throw new TypeError('Object.create unsupported, proto counld not be null');
  }
  function F() {}
  F.prototype = proto;
  return new F();
}


var person = {
  name: 'chenfuqiang',
  age: 18,
  friends: ['Bob', 'Mike']
}

var student = object(person);
student.grade = 6;

console.log(student.name);//chenfuqiang
console.log(student.age);//18
console.log(student.grade);//6
console.log(student.friend);//['Bob', 'Mike']

person.friends.push('Mary');
console.log(student.friends);//['Bob', 'Mike', 'Mary']
```

上面的 `student` 基于对象 `person` 创建，拥有了`person`所有的属性，由于本质上是原型链继承，所以所有对`person`对象的修改，都会影响到子类。


### 浅复制

如果不考虑复杂的原型，对对象的属性进行复制也可以实现继承。

```javascript
function extend(target, source) {
  for(var key in source) {
    if (source[key] !== 'undefined') {
      target[key] = source[key];
    }
  }
  return target;
}
var person = {
  name: 'chenfuqiang',
  age: 18,
  friends: ['Bob', 'Mike']
}
var student = extend({}, person);
student.grade = 6;

console.log(student.name);//chenfuqiang
console.log(student.age);//18
console.log(student.grade);//6
console.log(student.friend);//['Bob', 'Mike']

person.friends.push('Mary');
person.name = 'Lily';
console.log(student.friends);//['Bob', 'Mike']
console.log(student.name);//chenfuqiang
```

`student` 通过复制获取到了 `person` 的属性，由于是直接复制，当属性是引用类型时，复制的也是一个引用，如果对这个引用类型属性做修改时，会影响到所有指向这个对象的引用。

### 深复制

深复制基于浅复制的思想，只是在遇到对象或数组时，不再只是复制一个引用，而是进行递归复制。

```javascript
function extend(target, source) {
  target = target || {};
  for (var key in source) {
    if (typeof source[key] === 'object') {
      target[key] = (Array.isArray(source[key])) ? [] : {};
      extend(target[key], source[key]);
    } else if (source[key] !== 'undefined'){
      target[key] = source[key]
    }
  }
  return target;
}
var person = {
  name: 'chenfuqiang',
  age: 18,
  friends: ['Bob', 'Mike']
}
var student = extend({}, person);
student.grade = 6;

console.log(student.name);
console.log(student.age);
console.log(student.grade);
console.log(student.friend);

person.friends.push('Mary');
person.name = 'Lily';
console.log(student.friends);
console.log(student.name);
```

## 构造函数继承

### 原型链继承

原型链继承基于`javascript`的原型链继承机制，`javascript` 所有的对象都继承于 `Object`，我们先分析下这些对象是如何继承于 `Object` 的，看看对我们实现自定义继承有没有什么启发。

我们定义如下一个类 `SuperType`，它有一个属性 `superProperty` 和一个方法 `superMethod`。

```javascript
function SuperType() {
  this.superProperty = true;
}

SuperType.prototype.superMethod = function() {
  return 'super method';
};

var superType = new SuperType();
superType.toString(); //[object Object]
```

此时 `superType` 应该是继承于 `Object` 的，我们调用了定义在 `Object.prototype` 上的 `toString` 方法。那这是怎么实现的呢？我们画一个原型链示意图来看看。

{% asset_img prototype_inherit1.png %}

上图的红线指明了 `superType` 的原型链，所有定义在原型链上的属性都能被 `superType` 读取到，那现在需求来了，我们需要定义一个 `SubType` 继承于 `SuperType`, 一个很自然的想法就是我们要让 `SubType` 的实例的原型链包含 `SuperType` 实例的原型链。

{% asset_img prototype_inherit2.png %}

如上图所示，实现原型链继承的关键在于使 `SubType.prototype` 的原型 `__proto__` 指向 `SuperType.prototype`，如果对比上面两张图的化，我们会发现图一中 `SuperType` 的实例 `superType` 和 图二中 `SubType.prototype` 很类似，所以我们完全可以让 `SubType.prototype` 指向 `SuperType` 的一个实例，上代码。

```javascript
/**
 * 如果可以的话，为一个对象添加不可枚举的属性，否则添加普通属性
 * @param {Object} target 待添加属性的对象
 * @param {Object} properties key，value键值对，添加完成后，key为对象的属性名，value是对应的属性值
 */
function definePropertiesInnumerableIfPossible(target, properties) {
  for (const key in properties) {
    if (properties.hasOwnProperty(key)) {
      var value = properties[key];
      if (Object.defineProperty) {
        Object.defineProperty(target, key, {
          value: value,
          configurable: true,
          writable: true,
          enumerable: false
        });
      } else {
        target[key] = value;
      }
    }
  }
}
/**
  * 将proto设置为target的原型
  * @param {Object} target 目标对象
  * @param {Object} proto 原型对象
  */
function setPrototypeOf(target, proto) {
  if (Object.setPrototypeOf) {
    Object.setPrototypeOf(target, proto);
  } else {
    target.__proto__ = proto;
  }
}
function SubType() {
  this.subProperty = true;
}
SubType.prototype = new SuperType();
definePropertiesInnumerableIfPossible(SubType.prototype, {
  constructor: SubType,
  subMethod: function() {
    return 'sub method'
  }
});
setPrototypeOf(SubType, SuperType);
```

我们让 `SubType.prototype` 指向 `SuperType` 的一个实例，然后修复了 `SubType.prototype.constructor` 的指向并在上面定义了一些子类的方法，最后，我们将 `SuperType` 设置为 `SubType` 的原型，这样定义在 `SuperType` 上的方法，也能在 `SubType` 上调用，我们看看现在的原型链示意图。上面用到了两个工具方法，用于在es5以上的环境定义不可枚举的属性以及设置原型，下面会经常用到这两个工具函数，就不再声明了。

{% asset_img prototype_inherit3.png %}

经过这么一番折腾，我们实现了自己的目的，让 `SubType` 的实例的原型链包含 `SuperType` 实例的原型链，我们用代码验证下可行性。

```javascript
var superType  = new SuperType();
var subType = new SubType();

console.log(superType.superProperty); //true
console.log(superType.superMethod()); //'super method' 
console.log(subType.superProperty); //true
console.log(subType.superMethod()); //'super method' 
console.log(subType.subProperty); //true
console.log(subType.subMethod()); //'sub method'

console.log(superType instanceof SuperType); //true
console.log(subType instanceof SubType); //true
console.log(subType instanceof SuperType);//true

SuperType.colors = ['yellow'];
console.log(SubType.colors); //['yellow']
console.log(SuperType.isPrototypeOf(SubType)); //true
```

一切看起来很完美，子类可以调用父类的方法，`instanceof` 操作符也按预期工作，在父类上定义的类属性也可以直接被子类调用，但事实并没有想象中那么美好，仔细观察最后的原型链图，我们可以观察到，父类的属性 `superProperty` 定义在原型上，这使得它变成了一个公共属性，如果这个公共属性是引用类型的话，那么对这个引用类型的操作会影响到所有的子类，我们来验证下。

```javascript
function SuperType() {
  this.attr = {superProperty: true};
}

function SubType() {
  this.subProperty = true;
}
SubType.prototype = new SuperType();
definePropertiesInnumerableIfPossible(SubType.prototype, {
  constructor: SubType,
  subMethod: function() {
    return 'sub method'
  }
});
setPrototypeOf(SubType, SuperType);

var subType1 = new SubType();
var subType2 = new SubType();
subType1.attr.superProperty = false;
console.log(subType.attr.superProperty);//false
```

同时，如果父类的构造函数需要参数的话，由于调用父类构造函数的时候拿不到子类传递的参数，我们是没法进行传参的，因为存在这些弊端，所以这种方式在日常使用中一般不用，但是却给我们实现自定义继承提供了一个很好的思路。

### 借用构造函数继承

借用构造函数继承指在子类构造函数中调用父类的构造函数，并将上下文设置为当前 `this` 对象，这样父类定义的属性就能在子类上访问了，上代码。

```javascript
function Person(name, age, friends) {
  this.name = name;
  this.age = age;
  this.friends = friends;
  this.sayHello = function() {
    return `Hello, My name is ${this.name}, I'm ${this.age} years old`;
  };
  this.walk = function() {
    return "I'm walking";
  }
}

function Student(name, age, grade, friends) {
  Person.call(this, name, age, friends);
  this.grade = grade;
  this.sayHello = function() {
    return `Hello, My name is ${this.name}, I'm ${this.age} years old and in ${this.grade} grade now`;
  };
  this.study = function() {
    return 'I\'m studying';
  };
}

setPrototypeOf(Student, Person);
```

我们定义了一个父类 `Person` 类，初始化时传入三个参数`name`，`age` 和 `friends`，并定义了 `sayHello` 和 `walk` 方法，子类 `Student` 构造函数需要4个参数，初始化时，首先调用 `Person` 的构造函数，传入其中三个参数，然后在自身定义了 `grade` 属性，最后覆盖了 `Person` 的 `sayHello` 方法，并添加了一个 `study` 方法，我们写几个测试方法测试一下。

```javascript
var person = new Person('chenfuqiang', 18, ['Bob']);
var student = new Student('chenfuqiang', 18, 6, ['Bob', 'Mike']);

console.log(person.sayHello());//Hello, My name is chenfuqiang, I'm 18 years old
console.log(person.walk());//I'm working
console.log(person.friends);//['Bob']
console.log(student.sayHello());//Hello, My name is chenfuqiang, I'm 18 years old and in 6 grade now
console.log(student.walk());//I'm working
console.log(student.study());//I'm studying
console.log(student.friends);//['Bob', 'Mike']

console.log(person instanceof Person);//true
console.log(student instanceof Student);//true
console.log(student instanceof Person);//false

console.log(Person.isPrototypeOf(Student));//true

var student1 = new Student('chenfuqiang', 18, 6, ['Bob', 'Mike']);
student1.friends.push('Mary');
console.log(student1.friends);//['Bob', 'Mike', 'Mary']
console.log(student.friends);//['Bob', 'Mike']

console.log(student.sayHello === student1.sayHello);//false

Person.colors = ['yellow', 'black', 'white'];
console.log(Student.colors);//['yellow', 'black', 'white']
```

这种方式的缺点也很明显，由于单纯的调用构造函数，并没有建立子类和父类之间的联系，所以 `instanceof` 操作符在判断 `student` 和 `Person` 的时候返回了 `false`，同时，由于丢弃了原型，使得方法共用变成了不可能，所有的方法在每个实例上都必须重新定义一遍，这会比较浪费内存。这些缺点并不是那么致命，而且这种方法使用起来很简单，偶尔用一用我觉得还是可以的。

### 组合继承

取其长，补其短，组合继承并不是一个新的东西，他是上面两个继承方法组合起来一起使用，取每种方法的长处，摒弃美中方法的短处，直接上代码。

```javascript
function Person(name, age, friends) {
  this.name = name;
  this.age = age;
  this.friends = friends;
}

definePropertiesInnumerableIfPossible(Person.prototype, {
  sayHello: function() {
    return `Hello, My name is ${this.name}, I'm ${this.age} years old`;
  },
  walk: function() {
    return 'I\'m walking';
  }
});

function Student(name, age, grade, friends) {
  Person.call(this, name, age, friends);
  this.grade = grade;
}
Student.prototype = new Person();
definePropertiesInnumerableIfPossible(Student.prototype, {
  constructor: Student,
  sayHello: function() {
    return `Hello, My name is ${this.name}, I'm ${this.age} years old and in ${this.grade} grade now`;
  },
  study: function() {
    return 'I\'m studying';
  }
});
setPrototypeOf(Student, Person);
```

有了先前的经验，我们在定义 `Student` 类结合前面的优点，首先在 `Student` 类的构造方法里调用 `Person` 类的构造方法，并让 `Student.prototype` 指向 `Person` 类的一个实例，这样不仅将父类构造方法初始化的属性直接定义在子类上，并且建立了父类和子类之间的关系，使得子类的原型链包含父类的原型链。我们写几个方法测试下。

```javascript
var person = new Person('chenfuqiang', 18, ['Bob']);
var student = new Student('chenfuqiang', 18, 6, ['Bob', 'Mike']);

console.log(person.sayHello());//Hello, My name is chenfuqiang, I'm 18 years old
console.log(person.walk());//I'm working
console.log(person.friends);//['Bob']
console.log(student.sayHello());//Hello, My name is chenfuqiang, I'm 18 years old and in 6 grade now
console.log(student.walk());//I'm working
console.log(student.study());//I'm studying
console.log(student.friends);//['Bob', 'Mike']

console.log(person instanceof Person);//true
console.log(student instanceof Student);//true
console.log(student instanceof Person);//true

console.log(Person.isPrototypeOf(Student));//true

var student1 = new Student('chenfuqiang', 18, 6, ['Bob', 'Mike']);
student1.friends.push('Mary');
console.log(student1.friends);//['Bob', 'Mike', 'Mary']
console.log(student.friends);//['Bob', 'Mike']

console.log(student.sayHello === student1.sayHello);//true

Person.colors = ['yellow', 'black', 'white'];
console.log(Student.colors);//['yellow', 'black', 'white']
```

通过上面的测试代码，我们可以看到，现在子类可以调用父类的方法，也可以重写父类的方法，还可以添加自身的方法，`instanceof` 操作符也工作得很好，父类构造函数定义的属性不被所有子类共享，而定义在原型上的方法被所有子类共享，子类还可以访问父类的类属性，很完美对不对？其实不然，组合继承也有自己的缺点，仔细观察代码可以发现，将一个`Person`类的实例赋值给`Student.prototype` 时，调用 `Person` 的构造方法，且未传递任何参数，这会导致在 `Student.prototype` 上定义了一些值为 `undefined` 的父类属性，如果父类的属性不是通过参数获取的，则会在原型上定义一些无用的属性(因为这些属性在子类上会再定义一次，覆盖原型上的这些属性)，我们画个原型链示意图理解一下。

{% asset_img conbination_inherit.png %}

可以看到 `Student.prototype` 上定义的 `name`, `age`, `friends` 都是多余的，下面寄生式组合继承可以修复这个问题。

### 寄生式组合继承

寄生式组合继承基于组合继承修改而来，修复了子类构造函数的 `prototype` 重复定义构造函数中定义的属性，修复方法来自于上面的 `object` 方法, `object` 方法返回一个以第一个参数作为原型的对象。

```javascript
function object(proto) {
  if (typeof proto !== 'object') {
    throw new TypeError('proto is not a Object');
  }
  if (Object.create) {
    return Object.create(proto);
  }
  var F = function () {}
  F.prototype = proto;
  return new F();
}
```

寄生式组合继承主要思想是不调用父类的构造方法创建子类的原型对象，而是通过 `Object.create()` 方法或者一个空构造函数作为桥接，创建一个原型 `__proto__` 指向父类构造函数的 原型对象 `prototype`，上代码。

```javascript
function Person(name, age, friends) {
  this.name = name;
  this.age = age;
  this.friends = friends;
}
definePropertiesInnumerableIfPossible(Person.prototype, {
  sayHello: function() {
    return `Hello, My name is ${this.name}, I'm ${this.age} years old`;
  },
  walk: function() {
    return "I'm walking";
  }
});

function Student(name, age, grade, friends) {
  Person.call(this, name, age, friends);
  this.grade = grade;
}

Student.prototype = object(Person.prototype);
definePropertiesInnumerableIfPossible(Student.prototype, {
  constructor: Student,
  study: function() {
    return "I'm studying";
  },
  sayHello: function() {
    return `Hello, My name is ${this.name}, I'm ${this.age} years old and in ${this.grade} grade now`;
  }
});
setPrototypeOf(Student, Person);

var person = new Person('chenfuqiang', 18, ['Bob']);
var student = new Student('chenfuqiang', 18, 6, ['Bob', 'Mike']);

console.log(person.sayHello());//Hello, My name is chenfuqiang, I'm 18 years old
console.log(person.walk());//I'm working
console.log(person.friends);//['Bob']
console.log(student.sayHello());//Hello, My name is chenfuqiang, I'm 18 years old and in 6 grade now
console.log(student.walk());//I'm working
console.log(student.study());//I'm studying
console.log(student.friends);//['Bob', 'Mike']

console.log(person instanceof Person);//true
console.log(student instanceof Student);//true
console.log(student instanceof Person);//true

console.log(Person.isPrototypeOf(Student));//true

var student1 = new Student('chenfuqiang', 18, 6, ['Bob', 'Mike']);
student1.friends.push('Mary');
console.log(student1.friends);//['Bob', 'Mike', 'Mary']
console.log(student.friends);//['Bob', 'Mike']

console.log(student.sayHello === student1.sayHello);//true

Person.colors = ['yellow', 'black', 'white'];
console.log(Student.colors);//['yellow', 'black', 'white']
```

与组合继承唯一不同的是，组合继承 `Student.prototype` 指向 `Person` 类的一个实例，寄生式组合继承 `Student.prototype` 指向 `object` 方法的返回值，这个返回值以 `Person.prototype` 为原型，且不包含 `Person` 构造函数会初始化的值。画个原型链图理解一下，下面这张图中 `object` 使用的是空构造函数做桥接的方式。

{% asset_img parasitic_conbination_inherit.png %}

这个方法可以说是实现了自定义继承，并且没有什么嘈点，但是写起来可能会复杂一点，最后一个 `es6` 的 `class` 就一点也不复杂，而且还有点简单。

### es6 的 class 语法

最新的 `ECMAScript` 标准定义了 `class` 语法用于定义一个类，同时支持使用 `extends` 关键字实现继承，与上面这些方法比，优点是语法简单明了，缺点是浏览器支持较差，需要使用类似于 `babel` 之类的代码转换工具进行转换，我们看下代码是怎么写的。

```javascript
class Person {
  constructor(name, age, friends) {
    this.name = name;
    this.age = age;
    this.friends = friends;
  }
  sayHello() {
    return `Hello, My name is ${this.name}, I'm ${this.age} years old`;
  }
  walk() {
    return `I'm walking`;
  }
}

class Student extends Person {
  constructor(name, age, grade, friends) {
    super(name, age, friends);
    this.grade = grade;
  }
  sayHello() {
    return `Hello, My name is ${this.name}, I'm ${this.age} years old and in ${this.grade} grade now`;
  }
  study() {
    return 'I\'m studying';
  }
}

var person = new Person('chenfuqiang', 18, ['Bob']);
var student = new Student('chenfuqiang', 18, 6, ['Bob', 'Mike']);

console.log(person.sayHello());//Hello, My name is chenfuqiang, I'm 18 years old
console.log(person.walk());//I'm working
console.log(person.friends);//['Bob']
console.log(student.sayHello());//Hello, My name is chenfuqiang, I'm 18 years old and in 6 grade now
console.log(student.walk());//I'm working
console.log(student.study());//I'm studying
console.log(student.friends);//['Bob', 'Mike']

console.log(person instanceof Person);//true
console.log(student instanceof Student);//true
console.log(student instanceof Person);//true

console.log(Person.isPrototypeOf(Student));//true

var student1 = new Student('chenfuqiang', 18, 6, ['Bob', 'Mike']);
student1.friends.push('Mary');
console.log(student1.friends);//['Bob', 'Mike', 'Mary']
console.log(student.friends);//['Bob', 'Mike']

console.log(student.sayHello === student1.sayHello);//true

Person.colors = ['yellow', 'black', 'white'];
console.log(Student.colors);//['yellow', 'black', 'white']
```

我们使用 `es6` 语法实现上面的继承，代码量大大减少，之前的工具方法也不再需要了，默认所有的方法都是不可枚举的，之前实现的所有特性，测试方法全部测试通过(哈哈，其实我就是按照 `es6` 的特性去写测试方法的)。我们可以看到每个类都有一个 `constructor` 方法，这个方法被用做构造函数使用，如果子类继承了父类，必须通过 `super` 关键字调用父类的构造方法，是不是有点像构造方法借用，而子类和父类之间的联系，`es6` 会帮我们处理好，子类的原型是父类，子类会包含父类的所有属性和方法，其中构造函数初始化的属性赋值在子类的实例对象中，方法则存放在原型中，同时子类的原型继承父类的原型，写几个方法测试下。

```javascript
console.log(Person.isPrototypeOf(Student));//true
console.log(Person.prototype.isPrototypeOf(Student.prototype));//true
```

## 参考文档

- javascript 高级程序设计(第三版)
- [阮一峰网络日志-Javascript面向对象编程（三）：非构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance_continued.html)
- [阮一峰网络日志-Javascript面向对象编程（二）：构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html)
- [Object|MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object)
