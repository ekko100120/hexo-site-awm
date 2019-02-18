---
layout: post
title: css 布局总结
tags: ['css', 'layout']
category: ['front-end']
---

# css 布局总结

## 水平居中

### 适合定宽与不定宽

1. 父框设置`text-align: center`， 子框设置`display: inline-block; text-align:left`;

```html
<!-- 不定宽 -->
<div class="parent" style="text-align: center">
  <div class="child" style="display: inline-block; text-align: left;">
    Hello World!
  </div>
</div>
```

<div class="parent bg-secondary my-2" style="text-align: center">
    <div class="child bg-light text-dark" style="display: inline-block; text-align: left;">
      Hello World!
    </div>
</div>

```html
<!-- 定宽 -->
<div class="parent" style="text-align: center">
  <div class="child" style="display: inline-block; text-align: left; width: 300px;">
    Hello World!
  </div>
</div>
```

<div class="parent bg-secondary my-2" style="text-align: center">
    <div class="child bg-light text-dark" style="display: inline-block; text-align: left; width: 300px;">
      Hello World!
    </div>
</div>

2. 子框设置 `display: table; margin: auto`

```html
<!-- 不定宽 -->
<div class="parent">
    <div class="child" style="display: table; margin: auto;">
      Hello World!
    </div>
</div>
```

<div class="parent bg-secondary my-2">
    <div class="child bg-light text-dark" style="display: table; margin: auto;">
      Hello World!
    </div>
</div>

```html
<!-- 定宽 -->
<div class="parent">
    <div class="child" style="display: table; margin: auto; width: 300px;">
      Hello World!
    </div>
</div>
```

<div class="parent bg-secondary my-2">
    <div class="child bg-light text-dark" style="display: block; margin: auto; width: 300px;">
      Hello World!
    </div>
</div>

3. 父框设置 `position: relative;`, 子框设置 `position:absolute; left: 50%; transform:translateX(-50%);`。

<div class="parent bg-secondary my-2" style="position:relative;">
    <div class="child bg-light text-dark" style="position: absolute; left: 50%; transform: translateX(-50%);">
      Hello World!
    </div>
</div>

### 适合定宽

1. 子框设置  `margin: auto;`

```html
<!-- 定宽 -->
<div class="parent">
    <div class="child" style="margin: auto; width: 300px;">
      Hello World!
    </div>
</div>
```

<div class="parent bg-secondary my-2">
    <div class="child bg-light text-dark" style="margin: auto; width: 300px;">
      Hello World!
    </div>
</div>
