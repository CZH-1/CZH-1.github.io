---
title: "布局方案"
date: 2024-02-20 12:22:54
categories:
- HTML&CSS
tags:
- HTML&CSS
toc: true # 是否显示目录
---

> 布局方案 

<!-- more -->

## 水平垂直居中方案
### flex
适用场景：子元素宽高不固定、子元素宽高固定
```css
<div id="parent">
  <div id="children">HZFE</div>
</div>

#parent {
  background: red;
  height: 600px;
}
#children {
  background: blue;
}

#parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```
### transform
适用场景：子元素宽高不固定、子元素宽高固定
```css
#children {
  display: inline-block;
  position: relative;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
#children {
  width: 400px;
  height: 300px;
  position: relative;
  top: 50%;
  left: 50%;
  transform: translate(-200px, -150px);
}
```
## 两栏布局
两栏布局非常常见，往往是以一个定宽栏和一个自适应的栏并排展示存在
### float
```css
<style>
    .box{
        overflow: hidden; /* 添加BFC */
    }
    .left {
        float: left;
        width: 200px;
        background-color: gray;
        height: 400px;
    }
    .right {
        margin-left: 210px;
        background-color: lightgray;
        height: 200px;
    }
</style>
<div class="box">
    <div class="left">左边</div>
    <div class="right">右边</div>
</div>
```
### flex弹性布局
```css
<style>
    .box{
        display: flex;
    }
    .left {
        width: 100px;
    }
    .right {
        flex: 1;
    }
</style>
<div class="box">
    <div class="left">左边</div>
    <div class="right">右边</div>
</div>
```

## 三栏布局
实现三栏布局中间自适应的布局方式有：
* 两边使用 float，中间使用 margin
* 两边使用 absolute，中间使用 margin
* 两边使用 float 和负 margin
* display: table 实现
* flex实现
* grid网格布局

## 两边使用 float，中间使用 margin
```css
<style>
    .wrap {
        background: #eee;
        overflow: hidden; /* 生成BFC，计算高度时考虑浮动的元素 */
        padding: 20px;
        height: 200px;
    }
    .left {
        width: 200px;
        height: 200px;
        float: left;
        background: coral;
    }
    .right {
        width: 120px;
        height: 200px;
        float: right;
        background: lightblue;
    }
    .middle {
        margin-left: 220px;
        height: 200px;
        background: lightpink;
        margin-right: 140px;
    }
</style>
<div class="wrap">
    <div class="left">左侧</div>
    <div class="right">右侧</div>
    <div class="middle">中间</div>
</div>
```

## 两边使用 absolute，中间使用 margin
```css
<style>
  .container {
    position: relative;
  }
  
  .left,
  .right,
  .main {
    height: 200px;
    line-height: 200px;
    text-align: center;
  }

  .left {
    position: absolute;
    top: 0;
    left: 0;
    width: 100px;
    background: green;
  }

  .right {
    position: absolute;
    top: 0;
    right: 0;
    width: 100px;
    background: green;
  }

  .main {
    margin: 0 110px;
    background: black;
    color: white;
  }
</style>

<div class="container">
  <div class="left">左边固定宽度</div>
  <div class="right">右边固定宽度</div>
  <div class="main">中间自适应</div>
</div>
```

## flex实现
```css 

<style type="text/css">
    .wrap {
        display: flex;
        justify-content: space-between;
    }

    .left,
    .right,
    .middle {
        height: 100px;
    }

    .left {
        width: 200px;
        background: coral;
    }

    .right {
        width: 120px;
        background: lightblue;
    }

    .middle {
        background: #555;
        width: 100%;
        margin: 0 20px;
    }
</style>
<div class="wrap">
    <div class="left">左侧</div>
    <div class="middle">中间</div>
    <div class="right">右侧</div>
</div>
```
## 参考
* [元素水平垂直居中的方法有哪些](https://www.developers.pub/wiki/1065322/1067716#head3)
* [如何实现两栏/三栏布局，右侧自适应？](https://www.developers.pub/wiki/1065322/1067718#head10)

