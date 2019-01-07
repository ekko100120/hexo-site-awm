---
layout: post
title: javascript 类型转换
tags: javascript
date: 2018-12-17 19:19:04
category: ['javascript']
---

# javascript 类型转换

`javascript` 作为一门动态类型的脚本语言，我们在编写 js 代码的时候，往往会忽略变量的属性类型，这在大多数情况下不会出现问题，这是因为 js 为我们智能地做了隐式类型转换。但是在少数情况下，类型转换也会导致一些奇奇怪怪的问题，参考下面这段代码。

```javascript
//true
!!'1, 2';

//false
'1,2' == true;

//false
[1, 2] == true;

//true
'1,2' == [1, 2];
```

如果不熟悉 `javascript` 的类型转换规则，对于上述的结果可能会感到疑惑，为什么 `"1,2"` 强转成 `boolean` 值时是 `true`，与 `true` 进行比较时却是 `false`，一个字符串和一个数组进行比较，结果却是 `true`。在了解 `javascript` 的了类型转换规则后，这些问题自当迎刃而解。

## javascript 的数据类型

在讲 `javascript` 类型转换规则之前，先了解下 `javascript` 的数据类型， `javascript` 类型可以分为两大类，一个是原始类型，包含 `number`, `string`, `boolean`, `null` 和 `undefined`，还有一类是引用类型，所有的引用类型都是 `object` 类型的子类，包括 `array`, `function`以及 `javascript` 的一些内置对象，如 `Date`, `Math` 等。

## javascript 转换规则

### 原始转换到原始类型

1. 转换到 boolean
   下面列出的会被转换成 false，其他所有值都会转换成 true，后面的例子用到了 `!!` 将对象隐式转换成 `boolean`。

   - undefined
   - null
   - 0
   - -0
   - NaN
   - ""//空字符串

   ```javascript
   //false
   !!undefined;

   //false
   !!null;

   //false
   !!0;

   //false
   !!-0;

   //false
   !!NaN;

   //false
   !!'';

   //true
   !!Infinity;
   ```

2. 转换到 number
   `true` 转换成 1，`false` 转换成 0，`undefined` 转换成 NaN，`null` 转换成 0，空字符串转换成 0, 用数字表示的字符串转换成对应数字，有空格的情况下会自动去除空格，不能转换成直接转换成数字的则转换成 `NaN`,下面的例子使用算术运算符将其他类型隐式转换成 `number`。

   ```javascript
   //1
   +true;

   //0
   +false;

   //NaN，这里要和null区分开，null会转换成0
   +undefined;

   //0
   +null;

   //0
   +'';

   //12
   +'12';

   //12,有空格忽略空格
   +' 12';

   //NaN,含有非数字字符，转换成NaN;
   +' 12a';
   ```

3. 转换到 string
   转换到字符串的情况比较简单，直接将值加上个引号就是对应的转换值，下面的例子使用 `String` 构造函数进行转换。

   ```javascript
   //'undefined'
   String(undefined);

   //'null'
   String(null);

   //'true'
   String(true);

   //'false'
   String(false);

   //'0'
   String(-0);

   //'0'
   String(+0);

   //'1'
   String(1);
   ```

### 原始类型到对象的转换

所有原始类型转换成对象单纯的就是调用其对应的构造方法，`undefined` 和 `null` 例外，当试图将这两个值转换成对象时，会抛出一个类型错误（TypeError）。

### 对象到原始类型的转换

1. 转换到 `boolean`
   对象转换到 `boolean` 比较简单，所有对象都转换成 `true`，包括 `new Boolean(false)`，下面的例子用到了 `!!` 将对象隐式转换成 `boolean`。

   ```javascript
   //true
   !!new Boolean(false);

   //true
   !!{};

   //true
   !!new Date();
   ```

2. 转换到 `string`
   对象转换到 `string` 首先会调用从 `Object` 那继承来的 `toString` 方法，自己构造的对象的 `toString` 方法默认返回 `[object Object]`，如果 `toString` 方法返回的是原始值，则将这个原始值转换成字符串并返回，如果对象没有 `toString` 方法（或许被我们自己重写了）或者返回的不是原始值，则 `javascript` 会再尝试调用对象的 `valueOf` 方法，这个方法也是从 `Object` 那继承过来的，自己构造的对象这个方法默认返回自身，如果 `valueOf` 方法返回的时原始值，则将这个原始值转换成字符串并返回，如果对象没有 `valueOf` 方法或是这个方法返回的不是原始对象，则抛出一个类型转换错误。

   ```javascript
   var obj = {};
   obj.toString = () => 4;
   obj.valueOf = () => '3';
   //'4'
   String(obj);

   obj = {};
   obj.toString = undefined;
   obj.valueOf = () => '3';
   //'3'
   String(obj);

   obj = {};
   obj.toString = () => obj;
   obj.valueOf = () => '3';
   //'3'
   String(obj);

   obj = {};
   obj.toString = () => obj;
   obj.valueOf = () => obj;
   //throw a TypeError
   String(obj);
   ```

   上面的例子可以很好的证明上面的描述，我特意对两个方法的返回值的类型做了不一样的处理，第一轮调用中，`toString` 方法返回数值 4，直接转换为字符串 '4' 并返回。第二轮调用中，`toString` 方法不存在了，所以再次尝试调用 `valueOf` 方法，返回一个字符串 '3',直接拿来使用，第三轮方法类似，只是 `toString` 方法返回了自身，第四轮调用中，`toString` 方法和 `valueOf` 都返回了自身，并不是一个原始类型，所以会抛出一个类型转换错误。

3. 转换到 `number`
   对象转换到 `number` 和转换到 `string`很类似，只不过时调用方法的顺序换了一下，首先会调用 `valueOf` 方法，如果 `valueOf` 方法返回的是原始值，则将这个原始值转换成数值并返回，如果对象没有 `valueOf` 方法或者返回的不是原始值，则 `javascript` 会再尝试调用对象的 `toString` 方法，如果 `toString` 方法返回的时原始值，则将这个原始值转换成数值并返回，如果对象没有 `valueOf` 方法或是这个方法返回的不是原始对象，则抛出一个类型转换错误。

   ```javascript
   var obj = {};
   obj.toString = () => 4;
   obj.valueOf = () => '3';
   //3
   +obj;

   var obj = {};
   obj.toString = () => 4;
   obj.valueOf = undefined;
   //4
   +obj;

   var obj = {};
   obj.toString = () => 4;
   obj.valueOf = () => obj;
   //4
   +obj;

   obj = {};
   obj.toString = () => obj;
   obj.valueOf = () => obj;
   //throw a TypeError
   +obj;
   ```

   上面这个例子基于之前的改造的，只不过是调换了 `toString` 和 `valueOf` 的优先顺序，看官们看看结果就好了。

Note：
一些内置对象的 `toString` 和 `valueOf` 有自己的一套实现，如数组的 `toString` 方法会把数组的每一个元素转换成字符串，再使用逗号连接，`Function class` 的 `toString` 会将自己的定义转换成字符串。`Date class` 则会返回一个可被自己解析的字符串， `RegExp`的 `toString` 会转换成正则表达式直接量的字符串，`valueOf` 返回自 1970 年 1 月 1 日以来的毫秒数。

还有一个需要注意的地方是，在大多数情况下，类型转换的目标值都是很明显的，如在一些流程控制语句中，像 `if` 和 `while` 希望得到一个 `boolean` 值，则类型转换会向 `boolean` 值转换，一些算术运算符往往希望操作数是一个数值，如 \*，/等，这时候类型转换会向 `number` 值转换。
然而还有一小部分情况是类型转换目标值不那么明显，如 `+`，`==`， `<` 等，就拿 `+` 来说，`+` 既可以做加法，也可以做字符串的连接，当有一个操作数是对象时，这个对象应该转换成什么数值类型呢？其实 `javascript` 在这里有着一种特殊的处理方法，这里 `javascript` 会像转换数值一个处理这个对象，先尝试调用 `valueOf`,再尝试调用 `toString`,但是还是有一点区别的，这里 `javascript` 不知道要将这个值转换成什么类型，所以这里只做到转换到原始值，直接返回，不再将这个原始值转换到`number` 或 `string`，后面即使要再转换，则根据各个操作符的规则来定，这个后面会说到。还有一个特别的地方，就是 `Date` 对象，这个对象在遇到这种情况时，会表现得特别不一样，会先尝试调用 `toString` 方法，再尝试调用 `valueOf` 方法，我们直接看例子。

```javascript
var obj = {};
obj.valueOf = () => 4;
obj.toString = () => '3';
//5，返回数值4，和1做加法运算
1 + obj;

obj = {};
obj.valueOf = () => obj;
obj.toString = () => '3';
//13,这个obj转换成'3',和数值1相加做字符串相加操作
1 + obj;

obj = {};
obj.valueOf = undefined;
obj.toString = () => '3';
//13,这个obj转换成'3',和数值1相加做字符串相加操作
1 + obj;

obj = new Date();
//一串很大的数字
obj.valueOf();
//UTC时间字符串
obj.toString();
//1和UTC时间字符串做字符串连接，这也可以说明Date对象特立独行的表现(先调用toString方法)
1 + obj;
```

## 类型转换的时机

了解了 `javascript` 类型转换规则，再来聊聊啥时候 `javascript` 会做类型转换操作。

### 显式转换

显示转换比较简单，如果需要转换成原始对象，则调用不带 `new` 的内置对象构造方法。

```javascript
//'12'
String(12);

//'true'
String(true);

//false
Boolean('');

//true
Boolean(1);

//123
Number('123');

//NaN
Number(undefined);

var obj = {};
obj.toString = () => '4';
//'4'
String(obj);
```

可以看到上面的例子完全符合之前的转换规则，如果要转换成对象的话，只需要调用加了 `new` 关键字的构造方法就可以了，结果是其对应的包装类。

```javascript
//'12'对应的字符串包装类
new String(12);

//'true'对应的字符串包装类
new String(true);

//false对应的Boolean包装类
new Boolean('');

//true对应的Boolean包装类
new Boolean(1);

//123对应的数值包装类
new Number('123');

//NaN对应的数值包装类
new Number(undefined);

//{}
new Object(undefined);

//{}
new Object(null);

//{}
Object(undefined);

//3对应的数值包装类
new Object(3);

var obj = {};
obj.toString = () => '4';
//'4'对应的数值包装类
new String(obj);
```

这里有个特例要说明下，因为 `Object` 没有对应的原始类型，所以加不加 `new` 关键字都是一样的，而且前面说到 `undefined` 和 `null` 在尝试转换成对象时会抛出一个 `TypeError` 错误对象，上面例子的结果显然不符合预期，其实抛出错误是在一些隐式转换时的表现，如果是调用 `Object` 构造函数，则只是简单地返回一个空对象而已。有人又要问了，我怎么知道不加 `new` 返回的是原始类型，加了 `new` 返回的是对应的包装类型呢？其实我们调用一下 `typeof` 就能看出来，原始类型返回其对应的类型字符串，如'string'，对象则统一返回 `Object`，我们来写个例子。

```javascript
//number
typeof Number('12');

//Object
typeof new Number('12');
```

### 隐式转换

相比于显示转换，隐式转换要复杂的多，也是经常造成 bug 的元凶，不了解其机制的人，对于此类 bug 也难以理解。

1. 原始类型向对象转换。
   原始类型是不含属性和方法的，但是在 `javascript` 中我们经常会看到这样的代码

   ```javascript
   //4
   'Hello World!'.indexOf('o');
   ```

   这里其实做了从原始类型到封装类型的转换，`javascript` 判断你在某个原始类型上调用方法时，会先将这个原始类型转换成对应的包装类型，再调用其方法，将返回值返回，随即销毁这个对象。这也可以理解为啥原始类型的属性和方法都是只读的，并且不能添加新的属性。

   ```javascript
   var a = '123';
   a.len = 3;
   //undefined
   a.len;
   ```

   由于 `undefined` 和 `null` 没有对应的包装类型，在尝试调用其中的方法时，会抛出一个 `TypeError` 错误对象，与之前说的吻合。

   ```javascript
   var a = undefined;
   //throw a TypeError
   a.toString();

   a = null;
   //throw a TypeError
   a.toString();
   ```

2. 算术运算符转换成数值类型。
   除了 `+` 可以连接字符串以外，所有的算术运算符都期望得到一个数值类型的操作数，如一元操作符 `++`，二元操作符 `*`等，这里只讨论除了 `+` 以外的算术运算符，`+`有自己的一套转换规则，这个后面说。

   ```javascript
   //123
   +'123';

   //6
   '2' * '3';

   //2
   '6' / '3';
   ```

   上面的例子一元操作符 `+` 将一个字符串转换成数值非常简便，笔者在平常的开发中就是这么用的。

3. 逻辑运算符，流程控制语句转换成布尔值。
   逻辑运算符 `!`, `||`, `&&`以及一些流程控制语句会将值转换成布尔值。
   `！` 用于取反，调用两次可以很便捷的将一个值转换成其对应的布尔值。
   `||` 和 `&&` 会有意思一点，这两个操作符只判断值是真值还是假值，不做类型转换，直接返回操作数的运算结果，真值指的是 `true` 以及所有可以转换成布尔值 `true` 的值，假值指的是 `false` 以及所有转换到布尔值是 `false` 的值，这些值在前面列举过。举个例子，`javascript` 遇到 `||` 的时候，会先判断第一个操作数是真值还是假值，如果是真值，直接返回这个操作数，而不是返回 `true`,同时也不会去理会第二个操作数，这个特性被称作短路操作，即第二个操作数根本就不会被执行，如果第一个操作数是假值，则直接返回第二个操作数的结果，而不管第二个操作数是否是真值还是假值，这个操作符经常被用来做默认值处理。`&&` 操作符类似，当第一个操作数为假值时，会做短路处理，返回第一个操作数。
   一些流程控制语句如 `if`, `while` 等会尝试将分支判断中的内容转换成 boolean 值进行判断，转换规则和前面的一直，就不多说了。

   ```javascript
   //false,将一个数值转换成布尔值，规则和之前说的一样
   !!0;

   //true，将一个对象转换成布尔值
   !!{};

   //{},用||给一个变量设置默认值，第一个操作数是变量，第二个操作数是默认值，如果变量是假值的话，则将这个默认值赋值给这个变量
   a = a || {};

   //&&判断一个值是否存在，常用来做非空校验,如果a是假值，则不会读取a的exist变量，这样的程序健壮性更好
   if (a && a.exist) {
     doSomething();
   }

   //{}是真值，所以这个if分支永远会被执行
   if ({}) {
     doSomething();
   }
   ```

4. 关系运算符。
   一些关系运算符的情况看起来会复杂一些，如 `==`， `>`等，两边如果数据类型不一致，则会做相应的类型转换，这边要注意的是这里讨论的是不严格等于 `==`，因为严格等于不做类型转换，严格等于当发现类型不一致，直接返回 `false`,而不严格等于则会做适当的类型转换，不严格等于的比较规则如下。

   - 如果两个操作数类型相同，则按照严格相等来比较，结果与严格比较结果相同。
   - 如果类型不同，则按照一定规则做转换。
     - 如果一个为 `null`, 一个为 `undefined`,返回 true。
     - 如果两边都是原始值，则两边都转换成数字，再进行比较，如 "1" 转换成 1， `true` 也转换成 1。
     - 如果有一个值是对象，则按照之前的规则将对象转换成原始值，除了 `Date` 对象是调用 `toString` 方法外，其余对象都是先尝试调用 `valueOf` 再尝试调用 `toString`，并且一旦得到原始值后不再做类型转换，直接返回。
     - 得到对象转换的原始值后，再重头按规则进行比较，如果类型相同，则返回严格等于的结果，如果类型不相同，则全部转换成数字进行比较。

   `'>'`，`'<'`的情况很类似,这两个操作符既可以比较数值的大小，也可以比较字符串的字母表顺序，他们按照如下规则进行比较。

   - 如果两边都是字符串，则比较字母表顺序。
   - 如果有一个不是字符串，且两边都是原始值，则尝试将两个的值都转换成数值进行比较。
   - 如果操作数存在对象，则将对象转换成原始值，转换方式同上所述，除了 `Date` 对象是调用 `toString` 方法外，其余对象都是先尝试调用 `valueOf` 再尝试调用 `toString`，并且一旦得到原始值后不再做类型转换，直接返回
   - 转换后两边的操作数都是原始值，从头开始比较，如果两边都是字符串，则比较字母表顺序，如果有一个不是字符串，且两边都是原始值，则尝试将两个的值都转换成数值进行比较。

   ```javascript
   //true
   undefined == null;

   //true, true转换成1，故相等
   true == 1;

   //false，true转换成1，故不相等
   true == 2;

   var obj = {};
   obj.valueOf = () => '4';
   //true,先将obj转换成'4',再将字符串转换成4，这里转换调用的valueOf方法，这里toString返回的是默认值[object, Object]
   obj == 4;

   obj = new Date();
   obj.valueOf = () => '4';
   obj.toString = () => 3;
   //true,日期对象先尝试调用toString方法获取原始数值3，然后进行比较，然后将右边的操作数 '3' 转换成数值3，然后进行比较
   obj == '3';
   ```

5. `+` 运算符。

   `+` 号运算符和严格等于 `==` 类似，不过它更喜欢字符串，它按照如下规则进行操作。

   - 如果两边都是原始值，且有一个是字符串，则尝试将两边的操作数都转换成字符串，否则将两边的操作数都转换成数值进行操作。
   - 如果两边的操作数存在对象，则会先对对象进行转换，转换规则同`==`。
   - 转换后两边应该都是原始值了，则从头按照两边都是原始值的规则进行比较。

   ```javascript
   //'11',存在字符串，将两个值转换成字符串做连接操作
   1 + '1';

   //2,不存在字符串，则将两边的数都转换成数值进行加法操作
   true + true;

   var obj = {};
   obj.valueOf = () => 4;
   //5,先将对象转换成4，发现两边都是数值，做加法操作
   1 + obj;

   obj = {};
   obj.valueOf = () => obj;
   obj.toString = () => '4';
   //'14',先尝试调用valueOf方法，发现返回的不是原始值，再尝试调用toString方法，返回字符串'4',然后两边存在一个字符串，则做字符串连接操作
   1 + obj;
   ```

6. 存取对象或数组属性时将对象转换成字符串（2018.12.18 补充）
   在使用 `[]` 存取对象的属性或者数组的元素时，如果 `[]` 里是一个对象，则会尝试将对象转换成字符串。

   ```javascript
   var a = [1, 2, 3, 4];

   var b = {};
   b.toString = () => '1';
   b.valueOf = () => 2;
   //2，尝试将对象转换成字符串，先调用toString方法
   a[b];

   b = {};
   b.toString = () => b;
   b.valueOf = () => 2;
   //3，尝试将对象转换成字符串，先调用toString方法，toString返回了一个对象，继续调用valueOf方法
   a[b];

   b = {};
   b.toString = undefined;
   b.valueOf = () => 2;
   //3，尝试将对象转换成字符串，先调用toString方法，toString不存在，继续调用valueOf方法
   a[b];

   a = {
     key1: 'value1',
     key2: 'value2'
   };

   b = {};
   b.toString = () => 'key1';
   b.valueOf = () => 'key2';
   //'value1'，尝试将对象转换成字符串，先调用toString方法
   a[b];

   b = {};
   b.toString = () => b;
   b.valueOf = () => 'key2';
   //'value2'，尝试将对象转换成字符串，先调用toString方法，toString返回了一个对象，继续调用valueOf方法
   a[b];

   b = {};
   b.toString = undefined;
   b.valueOf = () => 2;
   //value2，尝试将对象转换成字符串，先调用toString方法，toString不存在，继续调用valueOf方法
   a[b];
   ```

## 回到开头的例子

```javascript
//true，这是一个真值，转换成布尔值后为true
!!'1,2';

//false，两边不全是字符串，转换成数值，true转换成1，'1,2'转换成NaN,故不相等，结果为false
'1,2' == true;

/**
 * false
 * 数组[1, 2]是对象，先尝试调用valueOf()方法，返回自身还是一个对象，
 * 再尝试调用toString()方法，返回'1,2'是一个字符串，是原始值
 * 将原始值'1,2'与true进行比较,结果同上
 */
[1, 2] == true;

/*
 * true
 * 数组[1, 2]是对象，先尝试调用valueOf()方法，返回自身还是一个对象，
 * 再尝试调用toString()方法，返回'1,2'是一个字符串，是原始值,
 * 将原始值'1,2'与'1,2'进行比较,两者类型相同，进行严格比较，返回true
 */
'1,2' == [1, 2];
```

## 参考文档

- javascript 权威指南中文版(第六版)
