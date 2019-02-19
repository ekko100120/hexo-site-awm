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

## 两列布局

### 一列定宽，一列自适应

<div class="content bg-secondary p-2" style="width: 360px; height: 270px;">
  <div class="left bg-light">
    left
  </div>
  <div class="right bg-light">
    right
  </div>
</div>

1. 右栏设置 `width: width; float: left;`, 右栏设置 `margin-left: width;`。

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

    <div class="content bg-secondary p-2" style="width: 360px; height: 270px;">
      <div class="left bg-light" style="float: left; width: 60px;">
        left
      </div>
      <div class="right bg-light" style="margin-left: 68px;">
        right
      </div>
    </div>

## 三列布局

### 圣杯布局

### 双飞翼布局

### 定位布局



