---
layout: post
title: css 布局总结
tags: ['css', 'layout']
category: ['front-end']
---

# css 布局总结

## 水平居中

### 子框自适应宽度

1. 父框设置`text-align: center`， 子框设置`display: inline-block; text-align:left`。

    ```html
    <div class="parent">
      <div class="child">
        Hello World!
      </div>
    </div>
    <style>
      .parent {
        text-align: center;
      }
      .child {
        display: inline-block; 
        text-align: left;
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="text-align: center">
        <div class="child bg-light text-dark" style="display: inline-block; text-align: left;">
          Hello World!
        </div>
    </div>

    特点：可兼容至IE8
2. 子框设置 `display: table; margin: auto`。

    ```html
    <div class="parent">
        <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .child {
        display: table;
        margin: auto;
      }
    </style>
    ```

    <div class="parent bg-secondary my-2">
        <div class="child bg-light text-dark" style="display: table; margin: auto;">
          Hello World!
        </div>
    </div>

    特点：可兼容至IE8

3. 父框设置 `position: relative;`, 子框设置 `position:absolute; left: 50%; transform:translateX(-50%);`。

    ```html
    <div class="parent">
        <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .parent {
        position:relative;
        height: 24px;
      }
      .child {
        position: absolute; 
        left: 50%; 
        transform: translateX(-50%);
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="position:relative; height: 24px">
        <div class="child bg-light text-dark" style="position: absolute; left: 50%; transform: translateX(-50%);">
          Hello World!
        </div>
    </div>

    特点：兼容性差，兼容到IE10，不好理解，需要手动撑开父框的高度。

4. 父框设置 `display: flex; just-content: center`。

    ```html
    <div class="parent">
        <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .parent {
        display: flex; 
        justify-content: center;
      }
    </style>
    ```
    <div class="parent bg-secondary my-2" style="display: flex; justify-content: center;">
        <div class="child bg-light text-dark">
          Hello World!
        </div>
    </div>

    特点：简单，兼容性较差。

5. 父框设置 `display: flex;`, 子框设置 `margin: auto`。

    ```html
    <div class="parent">
        <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .parent {
        display: flex;
      }
      .child {
        margin: auto;
      }
    </style>
    ```
    <div class="parent bg-secondary my-2" style="display: flex;">
        <div class="child bg-light text-dark" style="margin: auto;">
          Hello World!
        </div>
    </div>

    特点：简单，兼容性较差。

### 只适合子框定宽

1. 子框设置  `margin: auto;`。

    ```html
    <div class="parent">
        <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .child {
        margin: auto; 
        width: 300px;
      }
    </style>
    ```

    <div class="parent bg-secondary my-2">
        <div class="child bg-light text-dark" style="margin: auto; width: 300px;">
          Hello World!
        </div>
    </div>

    特点: 简单，兼容性好。

2. 父框设置 `position: relative;`, 子框设置 `position:absolute; left: 50%; margin-left: -half width;`。

    ```html
    <div class="parent">
        <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .parent {
        position: relative; 
        height: 24px;
      }
      .child {
        position: absolute; 
        left: 50%; 
        margin-left: -150px; 
        width: 300px;
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="position: relative; height: 24px">
        <div class="child bg-light text-dark" style="position: absolute; left: 50%; margin-left: -150px; width: 300px;">
          Hello World!
        </div>
    </div>

    特点： 不好理解，需要手动撑开父容器的高度，但兼容性极好。

## 垂直居中

### 适合子框自适应

1. 设置父框 `display: table-cell; vertical-align: middle;`。

    ```html
    <div class="parent">
       <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .parent {
        display: table-cell; 
        vertical-align: middle; 
        height: 180px;
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="display: table-cell; vertical-align: middle; height: 180px;">
       <div class="child bg-light text-dark">
          Hello World!
        </div>
    </div>

    特点：兼容性好。

2. 设置父框 `position: relative;`，子框设置 `position: absolute; top: 50%; transform: translateY(-50%)`。

    ```html
    <div class="parent">
       <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .parent {
        position: relative; 
        height: 180px;
      }
      .child {
        position: absolute; 
        top: 50%; transform: 
        translateY(-50%);
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="position: relative; height: 180px">
       <div class="child bg-light text-dark" style="position: absolute; top: 50%; transform: translateY(-50%);">
          Hello World!
        </div>
    </div>

    特点：不好理解，兼容性较差

3. 设置父框 `display: flex; align-items: center;`。

    ```html
    <div class="parent">
        <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .parent {
        display: flex; 
        align-items: center; 
        height: 180px;
      }
    </style>
    ```
    <div class="parent bg-secondary my-2" style="display: flex; align-items: center; height: 180px;">
        <div class="child bg-light text-dark">
          Hello World!
        </div>
    </div>

    特点：简单，兼容性较差。

### 只适合子框定高

1. 设置父框 `position: relative;`，子框设置 `position: absolute; top: 50%; margin-top: -half height;`;

    ```html
    <div class="parent">
       <div class="child">
          Hello World!
        </div>
    </div>
    <style>
      .parent {
        position: relative; 
        height: 180px;
      }
      .child {
        position: absolute; 
        top: 50%;  
        margin-top: -30px; 
        height: 60px;
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="position: relative; height: 180px">
       <div class="child bg-light text-dark" style="position: absolute; top: 50%;  margin-top: -30px; height: 60px">
          Hello World!
        </div>
    </div>

    特点：兼容性好。

## 垂直水平居中

### 子框自适应

1. 父框设置 `text-align: center; display: table-cell; vertical-align: middle;`，子框设置 `display: inline-block; text-align: left`。

    ```html
    <div class="parent">
      <div class="child">
        Hello World!
      </div>
    </div>
    <style>
      .parent {
        text-align: center; 
        display: table-cell; 
        vertical-align: middle; 
        width: 300px; 
        height: 300px;
      }
      .child {
        display: inline-block; 
        text-align: left;
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="text-align: center; display: table-cell; vertical-align: middle; width: 300px; height: 300px;">
      <div class="child bg-light text-dark" style="display: inline-block; text-align: left">
        Hello World!
      </div>
    </div>

    特点： 兼容性好。

2. 父框设置 `position: relative;`，子框设置 `position: absolute; left: 50%; top: 50%; transform: translate(-50%,-50%);`。

    ```html
    <div class="parent">
      <div class="child">
        Hello World!
      </div>
    </div>
    <style>
      .parent {
        postion: relative;
        height: 300px;
      }
      .child {
        position: absolute; 
        left: 50%; 
        top: 50%; 
        transform: translate(-50%,-50%);
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="position:relative; height: 300px;">
      <div class="child bg-light text-dark" style="position: absolute; left: 50%; top: 50%; transform: translate(-50%,-50%);">
        Hello World!
      </div>
    </div>

    特点：兼容性较差，兼容性到IE10。

3. 父框设置 `display: flex; justify-content: center; align-items: center;`。

    ```html
    <div class="parent">
      <div class="child">
        Hello World!
      </div>
    </div>
    <style>
      .parent {
        display: flex; 
        justify-content: center; 
        align-items: center; 
        height: 300px;
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="display: flex; justify-content: center; align-items: center; height: 300px;">
      <div class="child bg-light text-dark">
        Hello World!
      </div>
    </div>

    特点： 兼容性差。

### 子框定高定宽

1. 父框设置 `position: relative;`，子框设置 `position: absolute; left: 50%; top: 50%; margin-left: - half width; margin-top: - half height;`。

    ```html
    <div class="parent">
      <div class="child">
        Hello World!
      </div>
    </div>
    <style>
      .parent {
        postion: relative; 
        width: 300px; 
        height: 300px;
      }
      .child {
        position: absolute; 
        left: 50%; top: 50%; 
        height: 100px; 
        width: 100px; 
        margin-left: -50px; 
        margin-top: -50px;
      }
    </style>
    ```

    <div class="parent bg-secondary my-2" style="position:relative; width: 300px; height: 300px;">
      <div class="child bg-light text-dark" style="position: absolute; left: 50%; top: 50%; height: 100px; width: 100px; margin-left: -50px; margin-top: -50px">
        Hello World!
      </div>
    </div>

    特点：兼容性较差，兼容性到IE10。

## 多列布局

### 一列定宽，一列自适应

1. 浮动布局，右栏设置 `width: width; float: left;`, 右栏设置 `margin-left: width;`。

    ```html
    <div class="content">
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>
    <style>
      .left {
        float: left;
        width: 60px;
      }

      .right {
        margin-left: 68px;
      }
    </style>
    ```

    <div class="content bg-secondary p-2" style="width: 360px; height: 150px;">
      <div class="left bg-light" style="float: left; width: 60px;">
        left
      </div>
      <div class="right bg-light" style="margin-left: 68px;">
        right
      </div>
    </div>

    特点： 简单

2. table布局，父框设置 `display: table; table-layout: fixed;`，左栏设置 `width: width; display: table-cell;`，右栏设置 `display: table-cell;`。

    ```html
    <div class="content">
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>
    <style>
      .content {
        display: table;
        table-layout: fixed;
      }
      .left {
        display: table-cell;
        width: 100px;
      }
      .right {
        display: table-cell;
      }
    </style>
    ```

    <div class="content bg-secondary p-2" style="display: table; width: 360px; height: 150px;">
      <div class="left" style="display: table-cell; padding-right: 8px; width: 100px;">
        <div class="left-content bg-light h-100">
          left
        </div>
      </div>
      <div class="right bg-light" style="display: table-cell;">
        right
      </div>
    </div>

### 一列不定宽，一列自适应

1. 浮动布局，右栏设置 `float: left;`, 右栏设置 `overflow: auto;`。

    ```html
    <div class="content">
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>
    <style>
      .left {
        float: left;
      }

      .right {
        overflow: auto;
      }
    </style>
    ```

    <div class="content bg-secondary p-2" style="width: 360px; height: 150px;">
      <div class="left bg-light mr-2" style="float: left;">
        left
      </div>
      <div class="right bg-light" style="overflow: auto">
        right
      </div>
    </div>

    特点： 简单，同样适合定宽。

2. table布局，父框设置 `display: table;`，左栏设置 `width: 0.1%; display: table-cell;`，右栏设置 `display: table-cell;`。

    ```html
    <div class="content">
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>
    <style>
      .content {
        display: table;
      }
      .left {
        display: table-cell;
        width: 0.1%;
      }
      .right {
        display: table-cell;
      }
    </style>
    ```

    <div class="content bg-secondary p-2" style="display: table; width: 360px; height: 150px;">
      <div class="left" style="display: table-cell; padding-right: 8px; width: 0.1%">
        <div class="left-content bg-light h-100">
          left
        </div>
      </div>
      <div class="right bg-light" style="display: table-cell;">
        right
      </div>
    </div>

3. flex布局，父框设置 `display: flex;`，右栏设置 `flex: 1;`。

    ```html
    <div class="content">
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>
    <style>
      .content {
        display: flex;
      }
      .right {
        flex: 1;
      }
    </style>
    ```

    <div class="content bg-secondary p-2" style="display: flex; width: 360px; height: 150px;">
      <div class="left bg-light mr-2">
        left
      </div>
      <div class="right bg-light" style="flex: 1;">
        right
      </div>
    </div>

    特点：简单，但兼容性较差。

### 多列定宽，一列自适应

  1. 浮动布局，`float + margin`。

      ```html
      <div class="content">
        <div class="left bg-light mr-2" style="float: left; width: 80px; height: 88px; margin-right: 8px;">
          left
        </div>
        <div class="left bg-light mr-2" style="float: left; width: 80px; height: 68px; margin-right: 8px;">
          left
        </div>
        <div class="main bg-light" style="margin-left: 176px; height: 168px;">
          main
        </div>
      </div>
      <style>

      </style>
      ```

      <div class="content bg-secondary p-2" style="width: 360px; overflow: hidden;">
        <div class="left bg-light mr-2" style="float: left; width: 80px; height: 88px; margin-right: 8px;">
          left
        </div>
        <div class="left bg-light mr-2" style="float: left; width: 80px; height: 68px; margin-right: 8px;">
          left
        </div>
        <div class="main bg-light" style="margin-left: 176px; height: 168px;">
          main
        </div>
      </div>

      特点：兼容性好，父框高度由最右边的主栏决定，可通过清除浮动解决。

  2. table布局。

      ```html
      <div class="content">
        <div class="left">
          left
        </div>
        <div class="left">
          left
        </div>
        <div class="main">
          main
        </div>
      </div>
      <style>
        .content {
          display: table;
          table-layout: fixed;
        }
        .left {
          display: table-cell;
          width: 80px;
        }
        .main {
          display: table-cell;
        }
      </style>
      ```

      <div class="content bg-secondary p-2" style="display: table; width: 360px; table-layout: fixed;">
        <div class="left" style="display: table-cell; width: 80px; padding-right: 8px; height: 88px;">
          <div class="h-100 bg-light">
            left
          </div>
        </div>
        <div class="left" style="display: table-cell; width: 80px; padding-right: 8px; height: 68px;">
          <div class="h-100 bg-light">
            left
          </div>
        </div>
        <div class="main" style="display: table-cell; height: 168px;">
          <div class="h-100 bg-light">
            main
          </div>
        </div>
      </div>

      特点：简单，兼容性好，兼容至ie8，同时也是等高布局。

### 多列不定宽，一列自适应

  1. 浮动布局，`float + overflow`。

      特点：兼容性好，使用简单

  2. table布局。

      特点：兼容性好，使用简单，同时也是等高布局。

  3. flex布局。

      ```html
      <div class="content bg-secondary" style="display: flex; width: 360px;">
        <div class="main bg-light" style="flex: 1; order: 2; height: 168px;">
          main
        </div>
        <div class="left bg-light mr-2" style="flex: 0; order: 1; height: 88px;">
          left
        </div>
        <div class="left bg-light mr-2" style="flex: 0; order: 1; height: 68px;">
          left
        </div>
      </div>
      <style>
        .content: {
          display: flex;
        }
        .main {
          flex: 1;
          order: 2;
        }
        .left {
          flex: 0;
          order: 1;
        }
      </style>
      ```

      <div class="content bg-secondary" style="display: flex; width: 360px;">
        <div class="main bg-light" style="flex: 1; order: 2; height: 168px;">
          main
        </div>
        <div class="left bg-light mr-2" style="flex: 0; order: 1; height: 88px;">
          left
        </div>
        <div class="left bg-light mr-2" style="flex: 0; order: 1; height: 68px;">
          left
        </div>
      </div>

      特点：兼容性较差，简单易懂，可随意调换dom节点的位置。

## 等高布局

## 等分布局

## 三栏布局

  中间主栏，左右各一个边栏

### 浮动布局
  
左栏设置 `float: left;`，右栏设置 `float: right;`，主栏设置 `overflow: auto;`。

  ```html
  <div class="content">
    <div class="left">
      left
    </div>
    <div class="right">
      right
    </div>
    <div class="center">
      center
    </div>
  </div>
  <style>
    .content {
      overflow: auto;
    }
    .left {
      float: left;
    }
    .right {
      float: right;
    }
    .center {
      overflow: auto;
    }
  </style>
  ```

  <div class="content bg-secondary p-2" style="width: 360px; overflow: auto;">
    <div class="left bg-light mr-2" style="float: left; height: 210px;">
      left
    </div>
    <div class="right bg-light ml-2" style="float: right; height: 140px;">
      right
    </div>
    <div class="center bg-light" style="overflow: auto; height: 180px;">
      center
    </div>
  </div>

  特点：简单易理解，三栏全部自适应，但浏览器会先渲染边栏，再渲染主栏，父框高度由主栏说了算，可通过清除浮动解决。

### 圣杯布局

  ```html
  <div class="content">
    <div class="header">header</div>
    <div class="main">
      <div class="center">center</div>
      <div class="left">left</div>
      <div class="right">right</div>
    </div>
    <div class="footer">footer</div>
  </div>
  <style>
    .main {
      padding: 0 80px;
    }
    .center, .left, .right {
      position: relative; 
      float: left;
    }
    .center {
      width: 100%;
    }
    .left {
      margin-left: -100%; 
      left: -80px; 
      width: 80px;
    }
    .right {
      margin-left: -80px; 
      left: 80px; 
      width: 80px;
    }
  </style>
  ```

  <div class="content bg-secondary" style="width: 360px">
    <div class="header bg-light">header</div>
    <div class="main clearfix my-2" style="padding: 0 80px;">
      <div class="center" style="position: relative; float: left; width: 100%;">
        <div class="bg-light m-2" style="height: 168px"> center </div>
      </div>
      <div class="left" style="position: relative; float: left; margin-left: -100%; left: -80px; width: 80px;">
        <div class="bg-light m-2" style="height: 88px"> left </div>
      </div>
      <div class="right" style="position: relative; float: left; margin-left: -80px; left: 80px; width: 80px;">
        <div class="bg-light m-2" style="height: 68px"> right </div>
      </div>
    </div>
    <div class="footer bg-light">footer</div>
  </div>

  特点：左右栏定宽，左栏宽度不能大于中间栏宽度。

### 双飞翼布局

  ```html
  <div class="content">
    <div class="header">header</div>
    <div class="main">
      <div class="center-wrapper">
        <div class="center">
          center 
        </div>
      </div>
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>
    <div class="footer">footer</div>
  </div>
  <style>
    .center-wrapper {
      float: left; 
      width: 100%;
    }
    .center {
      margin: 0 80px;
    }
    .left {
      float: left; 
      margin-left: -100%; 
      width: 80px;
    }
    .right {
      float: left; 
      margin-left: -80px; 
      width: 80px;
    }
  </style>
  ```

  <div class="content bg-secondary" style="width: 360px">
    <div class="header bg-light">header</div>
    <div class="main clearfix my-2">
      <div class="center-wrapper" style="float: left; width: 100%;">
        <div class="center" style="margin: 0 80px;">
          <div class="bg-light m-2" style="height: 168px"> center </div>
        </div>
      </div>
      <div class="left" style="float: left; margin-left: -100%; width: 80px;">
        <div class="bg-light m-2" style="height: 88px"> left </div>
      </div>
      <div class="right" style="float: left; margin-left: -80px; width: 80px;">
        <div class="bg-light m-2" style="height: 68px"> right </div>
      </div>
    </div>
    <div class="footer bg-light">footer</div>
  </div>

  特点：左右栏定宽，解决了圣杯布局左栏宽度不能大于中间栏宽度的问题。

### 定位布局

  ```html
  <div class="content">
    <div class="header">header</div>
    <div class="main">
      <div class="center">
        center
      </div>
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>
    <div class="footer">footer</div>
  </div>
  <style>
    .main {
      position: relative;
    }
    .center {
      margin: 0 80px;
    }
    .left {
      position: absolute; 
      left: 0; 
      top: 0; 
      width: 80px;
    }
    .right {
      position: absolute; 
      right: 0; 
      top: 0; 
      width: 80px;
    }
  </style>
  ```

  <div class="content bg-secondary" style="width: 360px">
    <div class="header bg-light">header</div>
    <div class="main clearfix my-2" style="position: relative; overflow: hidden;">
      <div class="center" style="margin: 0 80px;">
        <div class="bg-light m-2" style="height: 168px"> center </div>
      </div>
      <div class="left" style="position: absolute; left: 0; top: 0; width: 80px;">
        <div class="bg-light m-2" style="height: 88px"> left </div>
      </div>
      <div class="right" style="position: absolute; right: 0; top: 0; width: 80px;">
        <div class="bg-light m-2" style="height: 68px"> right </div>
      </div>
    </div>
    <div class="footer bg-light">footer</div>
  </div>

  特点：简单，左右栏高度不允许超过中间栏的高度。

### 双飞翼改造

  利用 `box-size: border-box;` 使用外层的 `padding` 代替双飞翼内层的 `margin`。

  ```html
  <div class="content">
    <div class="header">header</div>
    <div class="main">
      <div class="center">
        center
      </div>
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>
    <div class="footer">footer</div>
  </div>
  <style>
    .center {
      float: left; 
      width: 100%;
      box-sizing: border-box;
      padding: 0 80px;
    }
    .left {
      float: left; 
      margin-left: -100%; 
      width: 80px;
    }
    .right {
      float: left; 
      margin-left: -80px; 
      width: 80px;
    }
  </style>
  ```

  <div class="content bg-secondary" style="width: 360px">
    <div class="header bg-light">header</div>
    <div class="main clearfix my-2">
      <div class="center" style="float: left; width: 100%; padding: 0 80px;">
        <div class="bg-light m-2" style="height: 168px"> center </div>
      </div>
      <div class="left" style="float: left; margin-left: -100%; width: 80px;">
        <div class="bg-light m-2" style="height: 88px"> left </div>
      </div>
      <div class="right" style="float: left; margin-left: -80px; width: 80px;">
        <div class="bg-light m-2" style="height: 68px"> right </div>
      </div>
    </div>
    <div class="footer bg-light">footer</div>
  </div>

  特点：在双飞翼的基础上改造，去除了需要额外嵌套的一层标签。

### flex 布局

  ```html
  <div class="content">
    <div class="header">header</div>
    <div class="main">
      <div class="center">
        center
      </div>
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>
    <div class="footer">footer</div>
  </div>
  <style>
    .main {
      display: flex;
    }
    .center {
      order: 2;
      flex: 1;
    }
    .left {
      order: 1;
      flex: 0;
    }
    .right {
      order: 3;
      flex: 0;
    }
  </style>
  ```

  <div class="content bg-secondary" style="width: 360px">
    <div class="header bg-light">header</div>
    <div class="main clearfix my-2" style="display: flex;">
      <div class="center" style="order: 2; flex: 1;">
        <div class="bg-light m-2" style="height: 168px"> center </div>
      </div>
      <div class="left" style="order: 1; flex: 0; width: 80px;">
        <div class="bg-light m-2" style="height: 88px"> left </div>
      </div>
      <div class="right" style="order: 3; flex: 0; width: 80px;">
        <div class="bg-light m-2" style="height: 68px"> right </div>
      </div>
    </div>
    <div class="footer bg-light">footer</div>
  </div>

  特点：简单，但兼容性不好。




