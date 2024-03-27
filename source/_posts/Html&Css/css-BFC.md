---
title: "对 BFC 的理解"
date: 2024-03-01 16:31:55
categories:
- HTML&CSS
tags:
- HTML&CSS
toc: true # 是否显示目录
---

> BFC 
<!-- more -->

## 一、概念
`BFC`（Block Formatting Context），即块级格式化上下文，它是页面中的一块渲染区域，并且有一套属于自己的渲染规则：

* 内部的盒子会在垂直方向上一个接一个的放置
* 对于同一个BFC的俩个相邻的盒子的margin会发生重叠，与方向无关。
* 每个元素的左外边距与包含块的左边界相接触（从左到右），即使浮动元素也是如此
* BFC的区域不会与float的元素区域重叠
* 计算BFC的高度时，浮动子元素也参与计算
* BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然

`BFC` 目的是形成一个相对于外界完全独立的空间，让内部的子元素不会影响到外部的元素
## 二、形成条件
触发 `BFC` 的条件包含不限于：

* 根元素，即HTML元素
* 浮动元素：float值为left、right
* overflow值不为 visible，为 auto、scroll、hidden
* display的值为inline-block、inltable-cell、table-caption、table、inline-table、flex、inline-flex、grid、inline-grid
* position的值为absolute或fixed
## 三、应用场景
利用 `BFC` 的特性，我们将 `BFC` 应用在以下场景：
### 防止margin重叠（塌陷）
```css
<style>
    p {
        color: #f55;
        background: #fcc;
        width: 200px;
        line-height: 100px;
        text-align:center;
        margin: 100px;
    }
</style>
<body>
    <p>Haha</p >
    <p>Hehe</p >
</body>
/* margin重叠100px 利用BFC修改为200px：*/
<style>
    .wrap {
        overflow: hidden;// 新的BFC
    }
    p {
        color: #f55;
        background: #fcc;
        width: 200px;
        line-height: 100px;
        text-align:center;
        margin: 100px;
    }
</style>
<body>
    <p>Haha</p >
    <div class="wrap">
        <p>Hehe</p >
    </div>
</body>

```
### 清除内部浮动
```css
<style>
    .par {
        border: 5px solid #fcc;
        width: 300px;
    }
    .child {
        border: 5px solid #f66;
        width:100px;
        height: 100px;
        float: left;
    }
</style>
<body>
    <div class="par">
        <div class="child"></div>
        <div class="child"></div>
    </div>
</body>
/* 父高度丢失 利用BFC修改为父元素包裹子元素 */
.par {
    overflow: hidden;
}
```
### 自适应多栏布局
```css
<style>
    body {
        width: 300px;
        position: relative;
    }
 
    .aside {
        width: 100px;
        height: 150px;
        float: left;
        background: #f66;
    }
 
    .main {
        height: 200px;
        background: #fcc;
    }
</style>
<body>
    <div class="aside"></div>
    <div class="main"></div>
</body>
/* 虽然.aslide为浮动元素，但是main的左边依然会与包含块的左边相接触  bfc修改为不会与浮动的.aside元素重叠 */
.main {
    overflow: hidden;
}

```

## 参考


