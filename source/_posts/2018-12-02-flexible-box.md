---
layout: post
title: flex 布局
date: 2018-12-02 19:12:28
tags: ['flex', 'css']
category: ['css']
---

# flex 布局

在做这个网站的时候，css 框架当时选择了 bootstrap4， 因为公司里用的就是 bootstrap3，觉得直接升级到4用来来问题应该不是很大。bootstrap4 相比3最大的变化就是大量使用 flex 布局，也就是弹性布局，用完之后，给我的感觉就是 flex 布局相比传统的基于盒模型的布局，真的是太强大了，在垂直居中和屏幕自适应上处理起来很优雅。bootstrap4 除了自己的组件使用了flex布局，还提供了一套基于 flex 布局的工具。本文参考阮一峰大大写的 [flex 布局教程](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)，flex布局语法部分是直接复制过来的。

## 浏览器兼容情况

{% asset_img flex-browser-support.jpg flex brower support %}

IE 只兼容 IE10+。

## Flex 布局是什么

Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。

任何一个容器都可以指定为 Flex 布局。

```css
.box{
  display: flex;
}
```

行内元素也可以使用 Flex 布局。

```css
.box{
  display: inline-flex;
}
```

Webkit 内核的浏览器，必须加上`-webkit`前缀。

```css
.box{
  display: -webkit-flex; /* Safari */
  display: flex;
}
```

注意，设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效。

## 基本概念

采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。
{% asset_img flex-main-concept.png flex main concept %}
容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。

项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。

## 容器的属性

以下6个属性设置在容器上。

* flex-direction
* flex-wrap
* flex-flow
* justify-content
* align-items
* align-content

### flex-direction 属性

flex-direction属性决定主轴的方向（即项目的排列方向）。

```css
.box {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

{% asset_img flex-direction.png %}

它可能有4个值。

* row（默认值）：主轴为水平方向，起点在左端。
* row-reverse：主轴为水平方向，起点在右端。
* column：主轴为垂直方向，起点在上沿。
* column-reverse：主轴为垂直方向，起点在下沿。

### flex-wrap 属性

默认情况下，项目都排在一条线（又称"轴线"）上。`flex-wrap`属性定义，如果一条轴线排不下，如何换行。

```css
.box{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```

它可能取三个值。

1. nowrap（默认）：不换行，如果排不下，则对每一项进行同级缩放
{% asset_img flex-wrap-nowrap.png %}

2. wrap：换行，第一行在上方, 如果排不下，各项不缩放。
{% asset_img flex-wrap-wrap.jpg %}

3. wrap-reverse：换行，第一行在下方。
{% asset_img flex-wrap-wrap-reverse.jpg %}

### flex-flow 属性

`flex-flow`属性是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap`。

### justify-content 属性

`justify-content` 属性定义了项目在主轴上的对齐方式。

```css
.box {
  justify-content: flex-start | flex-end | center | space-between | space-around;
}
```
{% asset_img flex-justify-content.png %}
它可能取5个值，具体对齐方式与轴的方向有关。下面假设主轴为从左到右。
* flex-start（默认值）：左对齐
* flex-end：右对齐
* center： 居中
* space-between：两端对齐，项目之间的间隔都相等。
* space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

### align-items 属性

`align-items` 属性定义项目在交叉轴上如何对齐。

```css
.box {
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```

{% asset_img flex-align-item.png %}
它可能取5个值。具体的对齐方式与交叉轴的方向有关，下面假设交叉轴从上到下。
* flex-start：交叉轴的起点对齐。
* flex-end：交叉轴的终点对齐。
* center：交叉轴的中点对齐。
* baseline: 项目的第一行文字的基线对齐。
* stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

### align-content 属性

`align-content` 属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

```css
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

{% asset_img flex-aligh-content.png %}
该属性可能取6个值。

* flex-start：与交叉轴的起点对齐。
* flex-end：与交叉轴的终点对齐。
* center：与交叉轴的中点对齐。
* space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
* space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
* stretch（默认值）：轴线占满整个交叉轴。

## 项目的属性

在项目上可以设置如下属性

* order
* flex-grow
* flex-shrink
* flex-basis
* flex
* align-self

### order 属性

`order` 属性定义项目的排列顺序。数值越小，排列越靠前，默认为0，同级按照书写顺序排列。

```css
.item {
  order: <integer>;
}
```

{% asset_img flex-order.png %}

### flex-grow 属性

flex-grow属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大, 这个属性用来做自适应布局很方便。

```css
.item {
  flex-grow: <number>; /* default 0 */
}
```

{% asset_img flex-grow.png %}
如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

### flex-shrink 属性

flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

```css
.item {
  flex-shrink: <number>; /* default 1 */
}
```

{% asset_img flex-shrink.jpg %}
如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。
负值对该属性无效。

### flex-basis 属性

flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。

```css
.item {
  flex-basis: <length> | auto; /* default auto */
}
```

它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。

### flex 属性

flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。

```css
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```
该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

### align-self 属性
`align-self` 属性允许单个项目有与其他项目不一样的对齐方式，可覆盖 `align-items` 属性。默认值为auto，表示继承父元素的`align-items`属性，如果没有父元素，则等同于stretch。

```css
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

{% asset_img flex-align-self %}
该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

## bootstrap flex 工具

上面介绍了 flex 布局的基本语法，在实际的使用过程中，如果要一个一个重复的写这些css属性，一定很烦对不对，好在 `bootstrap4` 提供了一套flex布局工具，可以让我们快速地进行页面布局。

### d-flex

 `d-flex` 用于创建一个 flexbox 容器

<div class="d-flex p-2 mb-2 border bg-secondary">I'm a flexbox container!</div>

```html
<div class="d-flex p-2 border bg-secondary">I'm a flexbox container!</div>
```

`d-inline-flex` 创建一个内联 flexbox 容器

<div class="d-inline-flex p-2 mb-2 border bg-secondary">I'm an inline flexbox container!</div>

```html
<div class="d-inline-flex p-2 border bg-secondary">I'm an inline flexbox container!</div>
```

`bootstrap4` 还提供了一系列响应式布局的class，使用方式为 `d-*-{value}`, 如
* .d-flex
* .d-inline-flex
* .d-sm-flex
* .d-sm-inline-flex
* .d-md-flex
* .d-md-inline-flex
* .d-lg-flex
* .d-lg-inline-flex
* .d-xl-flex
* .d-xl-inline-flex

### flex-{value}

`flex-{value}` 用于设置 `flex-direction` 的值。

`flex-row` 设置 `flex-direction` 的属性值为 row
<div class="d-flex flex-row bg-secondary mb-2">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex flex-row bg-secondary mb-2">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

`flex-column` 设置 `flex-direction` 的属性值为 column，
<div class="d-flex flex-column bg-secondary mb-2">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex flex-column bg-secondary mb-2">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

`flex-row-reverse` 设置 `flex-direction` 的属性值为 row-reverse
<div class="d-flex flex-row-reverse bg-secondary mb-2">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex flex-row-reverse bg-secondary mb-2">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

`flex-column-reverse` 设置 `flex-direction` 的属性值为 column-reverse
<div class="d-flex flex-column-reverse bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex flex-column-reverse bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

`bootstrap4` 提供了一系列响应式布局的class，使用方式为 flex-*-{value}。

### justify-content-{value}

`justify-content-{value}` 用于设置 `justify-content` 的属性值。控制项目在主轴上的对其方式，一共可以取五个值

* start
* center
* end
* between
* around

`justify-content-start` 设置 `justify-content` 属性值为 start。
<div class="d-flex justify-content-start bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex justify-content-start bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

`justify-content-center` 设置 `justify-content` 属性值为 center。
<div class="d-flex justify-content-center bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex justify-content-center bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

`justify-content-end` 设置 `justify-content` 属性值为 end。
<div class="d-flex justify-content-end bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex justify-content-end bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

`justify-content-between` 设置 `justify-content` 属性值为 space-between。
<div class="d-flex justify-content-between bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex justify-content-between bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

`justify-content-around` 设置 `justify-around` 属性值为 space-around
<div class="d-flex justify-content-around bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex justify-content-around bg-secondary">
    <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

bootstrap 提供了一系列响应式布局的class，使用方式为 `justify-content-*-{value}`。

### flex-wrap

`flex-wrap` 设置当容器剩余空间不足的时候，是否折行

<div class="d-flex flex-nowrap bg-secondary" style="width:10px">
  <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex flex-nowrap bg-secondary" style="width:100px">
  <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

<div class="d-flex flex-wrap bg-secondary" style="width:100px">
  <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex flex-wrap bg-secondary" style="width:100px">
  <p class="bg-light border p-2 mb-0">flex item1</p>
    <p class="bg-light border p-2 mb-0">flex item2</p>
    <p class="bg-light border p-2 mb-0">flex item3</p>
</div>
```

### align-items-{value}

`align-items-{value}` 用于设置 `aligh-items` 属性，可选值有 `start`, `end`, `center`, `baseline`, or `stretch`（浏览器默认值）。

`align-items-start` 设置 `align-items` 属性值为 `start`。
<div class="d-flex align-items-start bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex align-items-start bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>
```

`align-items-center` 设置 `align-items` 属性值为 `center`。
<div class="d-flex align-items-center bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex align-items-center bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>
```

`align-items-end` 设置 `align-items` 属性值为 `end`。
<div class="d-flex align-items-end bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex align-items-end bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>
```

`align-items-baseline` 设置 `align-items` 属性值为 `baseline`。
<div class="d-flex align-items-baseline bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex align-items-baseline bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>
```

`align-items-stretch` 设置 `align-items` 属性值为 `stretch`。
<div class="d-flex align-items-stretch bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex align-items-stretch bg-secondary mb-2" style="height: 100px">
    <p class="h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="h-75 bg-light border p-2 mb-0">flex item3</p>
</div>
```

### align-self-{value}

`align-self-{value}` 设置容器项目的 `align-self` 属性值，可以用来覆盖容器的 `align-items` 的属性。value 的可选值有 `start`, `center`, `end`, `baseline`, `stretch`(default).

`align-self-start` 设置 `align-self` 属性值为 `start`。
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-start h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-start h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-start h-75 bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-start h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-start h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-start h-75 bg-light border p-2 mb-0">flex item3</p>
</div>
```

`align-self-center` 设置 `align-slef` 属性值为 `center`。
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-center h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-center h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-center h-75 bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-center h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-center h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-center h-75 bg-light border p-2 mb-0">flex item3</p>
</div>
```

`align-self-end` 设置 `align-slef` 属性值为 `end`。
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-end h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-end h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-end h-75 bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-end h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-end h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-end h-75 bg-light border p-2 mb-0">flex item3</p>
</div>
```

`align-self-baseline` 设置 `align-slef` 属性值为 `baseline`。
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-baseline h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-baseline h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-baseline h-75 bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-baseline h-100 bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-baseline h-50 bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-baseline h-75 bg-light border p-2 mb-0">flex item3</p>
</div>
```

`align-self-stretch` 设置 `align-slef` 属性值为 `stretch`。
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-stretch bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-stretch bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-stretch bg-light border p-2 mb-0">flex item3</p>
</div>

```html
<div class="d-flex bg-secondary mb-2" style="height: 100px">
    <p class="align-self-stretch bg-light border p-2 mb-0">flex item1</p>
    <p class="align-self-stretch bg-light border p-2 mb-0">flex item2</p>
    <p class="align-self-stretch bg-light border p-2 mb-0">flex item3</p>
</div>
```

### flex-fill

`flex-fill` 设置所有的容器项目等比放大，充满整个容器。

<div class="d-flex bg-secondary mb-2">
  <div class="p-2 flex-fill bg-light border">Flex item</div>
  <div class="p-2 flex-fill bg-light border">Flex item</div>
  <div class="p-2 flex-fill bg-light border">Flex item</div>
</div>

### flex-grow-<number\>

`flex-grow-<number>` 设置容器项目的 `flex-grow` 属性值，设置项目占据剩余空间的比例, 值越大，占据的空间就越大。

<div class="d-flex bg-secondary mb-2">
  <div class="p-2 flex-grow-1 bg-light border">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border">Third flex item</div>
</div>

```html
<div class="d-flex bg-secondary mb-2">
  <div class="p-2 flex-grow-1 bg-light border">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border">Third flex item</div>
</div>
```

`boorstrap4` 提供了一系列用于响应式布局的 class, 使用格式为 `flex-*-grow-<number>`。


### flex-shrink-<number\>

`flex-shrink-<number>` 设置容器项目的 `flex-shrink` 属性值，当空间不足的时候，设置各项目缩放的比例，值越大，缩放的比例就越大。

<div class="d-flex bg-secondary">
  <div class="p-2 w-100 bg-light border">Flex item</div>
  <div class="p-2 flex-shrink-1 bg-light border">Flex item</div>
</div>

```html
<div class="d-flex bg-secondary">
  <div class="p-2 w-100 bg-light border">Flex item</div>
  <div class="p-2 flex-shrink-1 bg-light border">Flex item</div>
</div>
```

### auto margins

设置容器项目的 `margin` 属性值为 `auto`，如果有剩余的空间，可以使得容器想的外边距均分剩余的空间，在IE10和IE11上，要使用这个特性，不能设置容器的 `justify-content` 属性。

第一个块的右边距占满剩余空间
<div class="d-flex bg-secondary mb-2">
  <div class="p-2 bg-light border mr-auto">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
</div>

```html
<div class="d-flex bg-secondary mb-2">
  <div class="p-2 bg-light border mr-auto">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
</div>
```

第二个块的左边距占满剩余空间
<div class="d-flex bg-secondary mb-2">
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border ml-auto">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
</div>

```html
<div class="d-flex bg-secondary mb-2">
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border ml-auto">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
</div>
```

第一个块和第三个块均分剩余空间
<div class="d-flex bg-secondary mb-2">
  <div class="p-2 bg-light border mr-auto">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border ml-auto">Flex item</div>
</div>

```html
<div class="d-flex bg-secondary mb-2">
  <div class="p-2 bg-light border mr-auto">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border ml-auto">Flex item</div>
</div>
```

还可以配合`align-items`使用
<div class="d-flex align-items-end flex-column bg-secondary mb-2" style="height:200px">
  <div class="p-2 bg-light border mb-auto">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
</div>

```html
<div class="d-flex align-items-end flex-column bg-secondary mb-2" style="height:200px">
  <div class="p-2 bg-light border mb-auto">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
  <div class="p-2 bg-light border">Flex item</div>
</div>
```

### order-<number\>

`order-<number>` 设置容器项目的排列顺序，而不管他们的书写顺序，后面的number可以式1到12任意一个值，值越小，排列的优先级就越高
<div class="d-flex bg-secondary mb-2">
  <div class="order-2 p-2 bg-light border">First flex item</div>
  <div class="order-3 p-2 bg-light border">Second flex item</div>
  <div class="order-1 p-2 bg-light border">Third flex item</div>
</div>

```html
<div class="d-flex bg-secondary mb-2">
  <div class="order-2 p-2 bg-light border">First flex item</div>
  <div class="order-3 p-2 bg-light border">Second flex item</div>
  <div class="order-1 p-2 bg-light border">Third flex item</div>
</div>
```

### aligh-content-{value}

`align-content` 设置当容器内的项目在主轴上折行时，容器项目在副轴上是如何排列的，这个属性在容器内只有一行时没有效果。value一共可以取五个值 `start`,`center`,`end`,`between`, `around`。

`align-content-start`。
<div class="d-flex align-content-start flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>

```html
<div class="d-flex align-content-start flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>
```

`align-content-center`。
<div class="d-flex align-content-center flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>

```html
<div class="d-flex align-content-center flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>
```

`align-content-end`。
<div class="d-flex align-content-end flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>

```html
<div class="d-flex align-content-end flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>
```

`align-content-between`。
<div class="d-flex align-content-between flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>

```html
<div class="d-flex align-content-between flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>
```

`align-content-around`。
<div class="d-flex align-content-around flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>

```html
<div class="d-flex align-content-around flex-wrap bg-secondary mb-2" style="height: 100px;">
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
  <div class="p-2 bg-light border">flex item</div>
</div>
```

`bootstrap4` 提供了一系列用于响应式布局的class，使用语法为 align-content-*-{value}