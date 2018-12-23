---
layout: post
title: javascript 原型链
tags: ['javacript']
category: ['javascript']
---

# javascript 原型链

说到 `javascript` 面向对象技术，就免不了说到 `javascript` 的原型继承机制，众所周知的一个事实是 `javascript` 中的所有对象都继承于 `Object`，像一些通用的方法 `toString`，`valueOf`等都是从 `Object` 的原型对象（`Object.prototype`）中继承来的，当调用一个对象的 `toString`方法时，如果对象自身没有这个方法，则会从该对象的原型上查找，如果还是没有查找到这个方法，则继续在原型的原型上查找，这样就构成了一个原型链，由于在 `Object.prototype` 中定义了默认的 `toString` 方法，如果在原型链中都没有查找到这个方法，则会调用定义在 `Object.prototype` 中的 `toString` 方法。看下面这个例子

```javascript
var obj = {};
//[object, Object]，对象自身没有定义这个方法，javascript沿着原型链向上查找，最后调用了Object.prototype的toString()方法
obj.toString();

obj.toString = ()=> {
  return '{}';
}
//{}，对象自身定义了这个方法，javascript找到这个方法后，不再沿着原型链向上查找，直接调用
obj.toString();
```

## 原型和原型对象(__proto__与prototype)

在说 `javascript` 原型链的时候，有一个概念一定要理清楚，上面的叙述中特意用了两个名词，一个是原型，一个是原型对象，这两个完全是不同的概念。

这里有一个容易让人误解的地方，因为 `prototype` 直接翻译成中文就是原型，笔者在之前很长的一段时间里，都错以为 `obj` 的原型就是 `obj.prototype`，我相信很多人和我一样会有这样的误解，其实不然，如果没有给这个属性赋值的话，`obj.prototype`应该是`undefined`。`obj` 的原型其实是`obj`内部维护的一个指针，在 `ECMAScript5` 之前的标准中，并没有定义获取对象原型的方法，但一直以来浏览器厂商都有一个非标准的实现，`obj` 的 `__proto__` 属性就是其内部维护的指针，指向原型对象，在`ECMAScript5`标准中规范了获取对象原型的方法，可以使用 `Object.getPrototypeOf` 方法获取一个对象的原型。本文为了方便，就使用 `__proto__` 这个属性指代对象的原型了。

再来说说原型对象，原型对象是一个实实在在存在的对象，可以包含属性和方法，如果是通过 `new` 关键字和构造方法实例化对象的话，原型对象一般都是和构造函数(普通函数也有，但是一般没有意义)关联的一个对象，构造函数中的`prototype`属性指向这个原型对象，所以`Object.prototype`其实也是一个指针，这个指针指向原型对象，当通过 `new` 关键字实例化一个对象时，这个实例化对象的原型就指向构造函数关联的原型对象，上面这个例子，使用对象直接量创建的对象和使用 `new` 关键字调用`Object`构造函数效果是一样的，所以`obj`的原型 `__proto__` 和 `Object.prototype` 指向同一个原型对象，这个对象定义了一些通用的方法，如 `toString` 等。与构造函数关联的原型对象中一般还会有一个 `constructor` 属性，这个属性指向这个构造函数。

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

## Object.create() 的原型链

`ECMAScript5` 标准中定义了一个方法 `Object.create()` 用于创建对象，

## 内置对象Function的原型链

## instanceOf 操作符

