---
layout: post
title: javascript 类型转换
date: 
tags: javascript
---

# javascript 类型转换

`javascript` 作为一门动态类型的脚本语言，我们在编写js代码的时候，往往会忽略变量的属性类型，这在大多数情况下不会出现问题，这是因为js为我们智能地做了隐式类型转换。但是在少数情况下，类型转换也会导致一些奇奇怪怪的问题，参考下面这段代码。

```javascript
//result is true
console.log(!!"1, 2");

//result is false
console.log("1, 2" == true);

//result is false
console.log([1, 2] == true);

//result is true
console.log("1,2" == [1, 2]);
```

如果不熟悉 `javascript` 的类型转换规则，对于上述的结果可能会感到疑惑，为什么 `"1,2"` 强转成 `boolean` 值时是 `true`，与 `true` 进行比较时却是 `false`，一个字符串和一个数组进行比较，结果却是 `true`。在了解 `javascript` 的了类型转换规则后，这些问题自当迎刃而解。

## javascript 的数据类型

在讲 `javascript` 类型转换规则之前，先了解下 `javascript` 的数据类型， `javascript` 类型可以分为两大类，一个是原始类型，包含 `number`, `string`, `boolean`, `null` 和 `undefined`，还有一类是引用类型，所有的引用类型都是 `object` 类型的子类，包括 `array`, `function`以及 `javascript` 的一些内置对象，如 `Date`, `Math` 等。

## javascript 转换规则

### 原始转换到原始类型

1. 转换到 boolean
    下面列出的会被转换成false，其他所有值都会转换成true。
    * undefined
    * null
    * 0
    * -0
    * NaN
    * ""//空字符串

2. 转换到 number
`true` 转换成1，`false` 转换成0，`undefined` 转换成 NaN，`null` 转换成 0，空字符串转换成 0, 用数字表示的字符串转换成对应数字，有空格的情况下会自动去除空格，不能转换成直接转换成数字的则转换成 `NaN`。

3. 转换到 string
转换到字符串的情况比较简单，直接将值加上个引号就是对应的转换值，参考下表。

| 原始值 | 转换值 |
| ----- | ----- |
| undefined | 'undefined' |
| null | 'null' |
| true | 'true' |
| false | 'false' |
| -0 | '0' |
| +0 | '0' |
| 1 | '1' |

### 原始类型到对象的转换

所有原始类型转换成对象单纯的就是调用其对应的构造方法，`undefined` 和 `null` 例外，当试图将这两个值转换成对象时，会抛出一个类型错误（TypeError）。

### 对象到原始类型的转换

1. 转换到 `boolean`
    对象转换到 `boolean` 比较简单，所有对象都转换成 `true`，包括 `new Boolean(false)`。

2. 转换到 `string`
    对象转换到 `string` 首先会调用从 `Object` 那继承来的 `toString` 方法，自己构造的对象的 `toString` 方法默认返回 `[object Object]`，如果 `toString` 方法返回的是原始值，则将这个原始值转换成字符串并返回，如果对象没有 `toString` 方法（或许被我们自己重写了）或者返回的不是原始值，则 `javascript` 会再尝试调用对象的 `valueOf` 方法，这个方法也是从 `Object` 那继承过来的，自己构造的对象这个方法默认返回自身，如果 `valueOf` 方法返回的时原始值，则将这个原始值转换成字符串并返回，如果对象没有 `valueOf` 方法或是这个方法返回的不是原始对象，则抛出一个类型转换错误。
3. 转换到 `number`
    对象转换到 `number` 和转换到 `string`很类似，只不过时调用方法的顺序换了一下，首先会调用 `valueOf` 方法，如果 `valueOf` 方法返回的是原始值，则将这个原始值转换成数值并返回，如果对象没有 `valueOf` 方法或者返回的不是原始值，则 `javascript` 会再尝试调用对象的 `toString` 方法，如果 `toString` 方法返回的时原始值，则将这个原始值转换成数值并返回，如果对象没有 `valueOf` 方法或是这个方法返回的不是原始对象，则抛出一个类型转换错误。

Note：一些内置对象的 `toString` 和 `valueOf` 有自己的一套实现，如数组的 `toString` 方法会把数组的每一个元素转换成字符串，再使用逗号连接，`Function class` 的 `toString` 会将自己的定义转换成字符串。`Date class` 则会返回一个可被自己解析的字符串， `RegExp`的 `toString` 会转换成正则表达式直接量的字符串，`valueOf` 返回自1970年一月一日以来的毫秒数。

