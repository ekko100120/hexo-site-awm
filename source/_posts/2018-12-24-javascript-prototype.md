---
layout: post
title: javascript 原型链
tags:
  - javacript
  - prototype
category:
  - javascript
date: 2018-12-24 22:46:48
---


# javascript 原型链

说到 `javascript` 面向对象技术，就免不了说到 `javascript` 的原型继承机制，众所周知的一个事实是 `javascript` 中的所有对象都继承于 `Object`，像一些通用的方法 `toString`，`valueOf`等都是从 `Object` 的原型对象（`Object.prototype`）中继承来的，当调用一个对象的 `toString`方法时，如果对象自身没有这个方法，则会从该对象的原型上查找，如果还是没有查找到这个方法，则继续在原型的原型上查找，这样就构成了一个原型链，由于在 `Object.prototype` 中定义了默认的 `toString` 方法，如果在原型链中除`Object.prototype`对象之外都没有查找到这个方法，则会一直沿着原型链查找到`Object.prototype` 对象并调用其中的 `toString` 方法。反过来说，如果在原型链中的某个对象中定义了一些方法，则所有继承该原型对象的对象都继承了这些方法，这给 `javascript` 提供了极大的灵活性，在 `ECMAScript2015` 标准之前，我们也是通过原型链去实现自定义继承，其实 `ECMAScript2015` 也只是提供了一些语法糖，其内部实现机制还是基于原型链继承去实现的。

```javascript
var obj = {};
//[object, Object]，对象自身没有定义这个方法，javascript沿着原型链向上查找，最后调用了Object.prototype的toString()方法
obj.toString();

obj.toString = ()=> {
  return '{}';
}
//{}，对象自身定义了这个方法，javascript找到这个方法后，不再沿着原型链向上查找，直接调用
obj.toString();

//在原型对象上定义了一个方法(在实际业务开发中我们一般不会这么做)
Object.prototype.sayHello = function() {
  return 'Hello';
}
//Hello
obj.sayHello();
```

## 原型和原型对象(__proto__与prototype)

在说 `javascript` 原型链的时候，有一个概念一定要理清楚，上面的叙述中特意用了两个名词，一个是原型，一个是原型对象，这两个完全是不同的概念。

这里有一个容易让人误解的地方，因为 `prototype` 直接翻译成中文就是原型，笔者在之前很长的一段时间里，都错以为 `obj` 的原型就是 `obj.prototype`，我相信很多人和我一样会有这样的误解，其实不然，如果没有给这个属性赋值的话，`obj.prototype`应该是`undefined`。`obj` 的原型其实是`obj`内部维护的一个指针，在 `ECMAScript5` 之前的标准中，并没有定义获取对象原型的方法，但一直以来浏览器厂商都有一个非标准的实现，`obj` 的 `__proto__` 属性就是其内部维护的指针，指向原型对象，在`ECMAScript5`标准中规范了获取对象原型的方法，可以使用 `Object.getPrototypeOf` 方法获取一个对象的原型。本文为了方便，就使用 `__proto__` 这个属性指代对象的原型了。

再来说说原型对象，原型对象是一个实实在在存在的对象，可以包含属性和方法，如果是通过 `new` 关键字和构造方法实例化对象的话，原型对象一般都是和构造函数(普通函数也有，但是一般没有意义)关联的一个对象，构造函数中的`prototype`属性指向这个原型对象，所以`Object.prototype`其实也是一个指针，这个指针指向原型对象，当通过 `new` 关键字实例化一个对象时，这个实例化对象的原型就指向构造函数关联的原型对象，上面这个例子，使用对象直接量创建的对象和使用 `new` 关键字调用`Object`构造函数效果是一样的，所以`obj`的原型 `__proto__` 和 `Object.prototype` 指向同一个原型对象，这个对象定义了一些通用的方法，如 `toString` 等。与构造函数关联的原型对象中一般还会有一个 `constructor` 属性，这个属性指向这个构造函数，由于这个属性在整个原型链上，所以在 `obj` 对象上应该有一个 `constructor`　属性，它指向 `Object`。

{% asset_img javascript_prototype_1.png %}

这个示意图展示了 `javascript` 中最简单的原型链，图中的红线标明了 `obj` 的原型链，我们通过这个示意图再来分析一下原型链机制，当我们读取 `obj.x`时，`javascript`先从该对象中查找这个属性，`obj`中定义了这个属性，直接读取并返回，当我们调用 `toString` 方法时，`javascript` 先从本对象中查找 `toString` 方法，由于 `obj` 并未定义这个方法，所以 `javascript` 继续从 `obj` 的原型中查找，这个例子中，`obj` 的原型指向 `Object.prototype`，其中定义了 `toString` 方法，于是，`javascript` 就调用了这个方法，返回默认实现 `[object, Object]`。

从上面这个示意图我们还能看到，`Object.prototype`中有个 `constructor` 属性指向 `Object`, `Object.prototype` 的原型指向 `null`，所以我们可以说 `Object.prototype` 是所有原型链的顶端。

下面这段代码验证了上面这个示意图。

```javascript
var obj = {};
//true
obj.__proto__ === Object.prototype;
//true
Object.prototype.__proto__ === null;
//true
Object.prototype.constructor === Object;
```

## 自定义构造函数的原型链

我们升级一下难度，讨论一下自定义构造函数的原型链，如果对上面的概念理解的话，理解自定义构造函数的原型链应该不复杂，我们定义了一个 `Person` 类，并实例化了这个 `Person` 类。

```javascript
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

var person = new Person('Chen', 'Fuqiang');
```

{% asset_img javascript_prototype_2.png %}

我们可以看到，自定义构造函数实例化的对象的原型链相比于对象直接量创建的对象多了一层，这一层正是与 `Person` 构造函数关联的原型对象，该原型对象的原型指向 `Object.prototype`，这样就构成了继承关系，`Person` 类继承 `Object` 类, 默认所有的构造函数关联的原型对象的原型都是 `Object.prototype`，所以我们可以说 `javascript` 所有的对象都继承于 `Object`，或者说是 `Object` 的子类。
回到 `Person` 构造函数，与 `Object` 相似，`Person` 构造函数的 `prototype` 属性指向与 `Person` 构造函数的关联原型对象，原型对象中的 `constructor` 属性指向 `Person` 构造函数，`person` 实例的原型 `__proto__` 指向`Person.prototype`, `person` --> `Person.prototype` --> `Object.prototype` --> `null` 构成了 `person` 的原型链，示意图中已经用红线标示出来了。下面这段代码验证了上面这个示意图。

```javascript
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

var person = new Person('Chen', 'Fuqiang');
//true
person.__proto__ === Person.prototype;
//true
Person.prototype.constructor === Person;
//true
Person.prototype.__proto__ === Object.prototype;
```

基于这个特性，我们经常在定义一些实例方法时，将实例方法定义在与构造函数关联的原型对象中，而不是在构造函数中定义在每个实例中，这样可以避免重复实例方法，比如下面这个示例我们在 `Person` 关联的原型对象中定义了 `sayHello` 方法。

```javascript
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

Person.prototype.sayHello = function() {
  return `${this.firstName} ${this.lastName}`
}

var person = new Person('Chen', 'Fuqiang');
```

{% asset_img javascript_prototype_3.png %}

这里还有一个比较好玩的地方，由于 `javascript` 一切皆对象，所以 `Person` 构造函数也是一个对象，也拥有自己的原型链，那构造函数这个对象是谁的子类呢，很明显，是 `Function` 的子类，我们把上面这个示意图拓展下。

{% asset_img javascript_prototype_4.png %}

可以看到由于`Person`　构造函数继承于 `Function`, 所以 `Person` 的原型 `__proto__` 指向 `Function.prototype`，在 `Function.prototype` 中也有一个 `constructor` 属性指向 `Function`。由于 `Function.prototype` 又是 `Object` 的实例，所以 `Function.prototype` 的原型 `__proto__` 指向 `Object.prototype`，我们用代码验证下。

```javascript
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}
//true
Person.__proto__ === Function.prototype;
//true
Function.prototype.constructor === Function;
//true
Function.prototype.__proto__ === Object.prototype;
```

## Object.create() 的原型链

`ECMAScript5` 标准中定义了一个方法 `Object.create()` 用于创建对象，该方法接受两个参数，第一个参数是返回对象的原型，第二个参数是可选的，用于定义返回对象自身属性的属性描述对象，与`Object.defineProperties()` 的方法的第二个参数一致，由于这边只考虑原型链，我们暂且忽略这个参数，只关注第一个参数，由于第一个参数是返回对象的原型，所以返回对象的 `__proto__` 属性应该指向这个对象，我们来验证下。

```javascript
var a = {x:1};

var b = Object.create(a);
//true
b.__proto__ === a;
```

由于原型对象 `a` 中没有定义 `constructor` 属性，当我们试图获取 `b.constructor` 属性时，由于在 `a` 中找不到这个属性，`javascript` 沿着原型链一直向上找，最终在 `Object.prototype`　中找到了这个属性，这个属性指向 `Object`，我们继续验证下。

```javascript
var a = {x:1};

var b = Object.create(a);
//true
b.constructor === Object;
```

最后上个示意图

{% asset_img javascript_prototype_5.png %}


## 内置对象Function与Object的原型链

上面所说的已经讨论了大多数情况，包括对象直接量，自定义构造函数，以及 `Object.create` 方法构造出的对象，其实还有一种对象我们没有讨论，那就是 `javascript` 内置对象，我们都知道，`javascript` 帮我们内置了几个常用对象，包括几个原始类型的包装类，数组类 `Array` 以及工具类 `Math` 等，这几个类的在原型链上的外在表现其实与自定义构造函数的外在表现是一样的，我们可以认为内置对象是`javascript`帮我们内置了几个构造函数，在 `javascript` 的所有内置对象中，也包含着几个例外，他们的原型链很特殊，特殊到有必要单独拿出来讲下，他们是所有函数的父类 `Function` 以及所有对象的父类 `Object`。

我们来一个一个分析，首先是 `Function` 类，`Function` 类本身也是一个构造函数，`Function` 是自身的一个实例，是自身的一个实例，所以 `Function` 的原型 `__proto__` 应该指向 `Function.prototype`, 在`Function` 中，它的 `__proto__` 与 `prototype`　同时指向同一个对象，这个是 `Function` 本身最特殊的地方，然后还有一些通用规则，`Function.prototype.constructor` 应该指向 `Function`, `Function.prototype.__proto__`应该指向 `Object.prototype`。

再来分析一下 `Object`, `Object` 本身也是一个构造函数，所以`Object.__proto__` 应该指向 `Function.prototype`。

写个代码验证下

```javascript
//true
Function.__proto__ === Function.prototype;
//true
Function.prototype.constructor === Function;
//true
Object.__proto__ === Function.prototype;
//true
Function.prototype.__proto__ === Object.prototype;
```

再来个示意图理解一下

{% asset_img javascript_prototype_6.png %}


## instanceof 操作符

`instanceof` 操作符常常用于判断某个对象是否是一个对象的实例，举个实际的栗子。

```javascript
function Person () {

}
var person = new Person();
//true
person instanceof Persion;
//true
person instanceof Object;
```

因为 `person` 是 `Person` 的一个实例，所以上面这个表达式返回 `true`，那这个又和原型链有什么关系呢，其实他们的关系大了去了，`instanceof` 左边的操作数是要判断的对象，右边是一个构造函数对象，`javascript` 在处理 `instanceof` 操作符时其实是在判断这个对象的原型链上的某个对象是否和该构造函数关联的原型对象指向同一个对象，这其实等价于下面这段代码。

```javascript
function myInstanceof(o, O) {
  var proto = o.__proto__;
  var prototype = O.prototype;
  while(proto) {
    if (proto === prototype) {
      return true;
    } else {
      proto = proto.__proto__;
    }
  }
  //如果代码走到这里，说明原型链上没有一个对象等于O.prototype，返回false
  return false;
}
```

上面这段代码第一个参数是要检验的对象，第二个参数是一个构造函数，该方法会沿着原型链一直向上查找，直到查找到原型链顶端，如果还没查找到，则返回 `false`，我们来验证下这个函数与`instanceof` 操作符的等价性。

```javascript
function Person () {

}
var person = new Person();
//true
person instanceof Person;
//true
person instanceof Object;
//true
myInstanceof(person, Person);
//true
myInstanceof(person, Object);
/**
 * 这里改变了 person 对象的原型指向
 * 设置 prototype 是 person 对象的原型，并且是原型链的顶端
 * 构造了 O 对象，使其 prototype 属性指向 prototype
 */
var prototype = {};
prototype.__proto__ = null;
var O = function () {};
O.prototype = prototype;
person.__proto__ = prototype;
//false
person instanceof Person;
//false
person instanceof Object;
//true
person instanceof O;
//false
myInstanceof(person, Person);
//false
myInstanceof(person, Object);
//true
myInstanceof(person, O);
```

## 与原型相关的几个有用的方法

最后在介绍几个和原型相关的一些方法

### Object.getPrototypeOf(obj)

`Object.getPrototypeOf(obj)` 定义在 `ECMAScript5` 标准中，这个方法用于获取一个对象的原型，在 `ECMAScript5` 之前，并没有获取对象原型的标准方法，但是一直在浏览器厂商（IE除外）中都有一个非标准的实现，那就是对象的 `__proto__` 属性指向当前对象的原型对象，本文中也是一直用的这个属性，但是由于是非标准的属性，在实际业务中，我们一般不会这么去获取对象的原型。`Object.getPrototypeOf()` 接受一个参数，就是要获取原型的对象。

```javascript
function Person () {

}
var person = new Person();
//true
Object.getPrototypeOf(person) === Person.prototype;

//ECMAScript5 中会抛出一个 TypeError，ECMAScript5会返回 String.prototype
Object.getPrototypeOf('123');
//TypeError
Object.getPrototypeOf(null);
//TypeError
Object.getPrototypeOf(undefined);
```

在 `ECMAScript5` 标准中，如果传入的参数不是一个对象比如原始对象和 `null`，则会抛出一个 `TypeError`，在 `ECMAScript2015` 标准中如果传入的是原始对象类型，则会将该原始类型转换成对应的包装类型，但是 `null` 和 `undefined` 依旧会抛出一个 `TypeError`。

### Object.setPrototypeOf(obj, proto)

`Object.setPrototypeOf(obj, proto)` 定义在 `ECMAScript2015` 标准中，用于设置对象的原型，该方法接受两个参数，第一个参数是要设置原型的对象，第二个参数是要设置的原型对象。

```javascript
var o = { y: 1 };
var a = { x: 1 };
Object.setPrototypeOf(a, o);
//true
Object.getPrototypeOf(a) === o;
//1,从原型中获取到了这个属性
a.y;
//TypeError,第二个参数只能是object和null
Object.setPrototypeOf(a, '213');
Object.setPrototypeOf(a, undefined);
//TypeError,第一个参数不能是undefined和null
Object.setPrototypeOf(null, o);
Object.setPrototypeOf(undefined, o);
//默认失败，第一个参数是原始类型的时候
var b = 13;
Object.setPrototypeOf(b, o);
//true
Object.getPrototypeOf(b) === Number.prototype;
```

`Object.setPrototypeOf(obj, proto)` 方法在某些情况下会抛出 `TypeError` ，该方法的第一个参数不能是 `undefined` 和 `null`，第二个参数只能是 `object` 和 `null`。如果第一个参数是原始类型，则默认什么都不做。

### Object.prototype.isPrototypeOf(obj)

`Object.prototype.isPrototypeOf(obj)` 在 `ECMAScript3` 中被纳入标准，注意该方法并没有挂载在 `Object` 上，而是挂载在 `Object.prototype` 中，这意味着我们可以在任何`Object` 的子类中调用这个方法，该方法用于判断一个对象是不是另一个对象的原型。

```javascript
var o = { y: 1 };
var a = { x: 1 };
//false
o.isPrototypeOf(a);
//把o设置为a的原型
Object.setPrototypeOf(a, o);
//true
o.isPrototypeOf(a);
```

## 总结

最后总结一下，如果对象a是构造函数ClassA的实例，那么a对象内部会维护一个指针，该指针是该对象的原型，默认指向与构造函数ClassA关联的原型对象`ClassA.prototype`，如果原型对象还有自己的原型，这样就构成了一条原型链，在这条原型链上的所有属性与方法，在对象a都可以获取到，同时，对象a中还可以通过定义自身属性用于覆盖原型链上的方法，这给 `javascript` 提供了极大的灵活性，一般来说，`ClassA.prototype`中还会有一个`constructor`。
`instanceof` 操作符用于判断某个对象是不是某个构造函数的实例，该操作符的左操作数时一个对象实例，右操作数是一个构造函数，这里构造函数只是某个对象的外在表现，`javascript` 在处理 `instanceof` 操作符的时候，其内部是判断某个对象的原型链中的某个对象是否指向构造函数关联的原型对象。
除了构造函数的情况，在 `ECMAScript5` 标准里，我们还可以根据自身的需求去设置，获取和判断对象的原型，包括`Object.create`, `Object.setPrototypeOf`, `Object.prototype.isPrototypeOf`等方法。

下面是上面所有示意图的整合，如果上面所说的都理解了，下面这张图理解起来应该不难。

{% asset_img javascript_prototype.png %}

## 参考文档

- javascript 权威指南中文版(第六版)
- [javascript标准库](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects)

