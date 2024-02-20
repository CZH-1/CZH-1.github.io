---
title: "常见知识点汇总"
date: 2022-03-12 12:22:54
categories:
- 其他
tags:
- 其他
password: 123
message: 请输入密码查看详细内容
toc: true # 是否显示目录
---
常见知识点汇总

<!-- more -->

## 1. js 创建对象的方式

一. 使用 {} 字面量创建
```bash
# 等同于 new Object()
let obj = {
  name: '张三',
  sayHi: function () {
    console.log('hi~');
  }
}

```

二. 使用 new Object()
```bash
let obj = new Object()
obj.name = '张三'
obj.sayHi = function () {
  console.log('hi~' + o.name);
}
```

三. 使用工厂模式
```bash
function createObject(name){
  let o = new Object{}
  o.name = name
  o.sayHi = function () {
    console.log('hi~' + o.name);
  }
  return o
}
let obj = createObject('张三')
```

四. 使用构造函数

所谓构造函数，也是普通的函数，不过约定俗成，构造函数的名称首字母大写，普通函数的首字母小写。通过new 构造函数来创建对象。
```bash
function CreateObject(name){
  this.name = name 
  this.sayHi = function () {
    console.log('hi~' + o.name);
  }
}
let obj = new CreateObject('张三')
```

五. 使用原型模式

```bash
function CreateObject() { }
# createObject.prototype.name = '张三';
# createObject.prototype.sayHi = function () { 
# console.log('hi~' + o.name);
# };

# 优化
CreateObject.prototype = {
    constructor: CreateObject,
    name: '张三',
    sayHi: function () {
      console.log('hi~' + o.name);
    }
};

var a1 = new CreateObject();

a1.sayHi();
```
六. 组合模式

构造函数模式与原型模式双剑合璧。
```bash
function CreateObject(name) {
    this.name = name;
}

CreateObject.prototype = {
    constructor: CreateObject,
    sayHi: function () {
    console.log('hi~' + this.name);
    }
};

var person1 = new CreateObject('张三');
```


## 2. React 和 Vue 的区别

相同点：
  * 数据驱动
  * 虚拟 DOM
  * 组件化
  
不同点：
  * JSX 与模板语法
    * Vue：默认使用基于 HTML 的模板语法，将模板、样式及逻辑划分开来使关注点分离。也可以选配 JSX 支持。
    * React：默认使用 JSX 编写组件，将 HTML 和 CSS 组合到 JavaScript 中。在选用 TypeScript 作为开发语言时，可以更方便的整合相关工具链。
  * 数据响应模型
    * Vue 是推模型，当数据改动时，界面会自动更新。Vue 通过 defineProperty 监听数据的改动，可以做到在数据改变时，精准细粒度的更新对应的视图。
    * React 是拉模型，当数据改动时，需要手动调用 setState 更新界面。React 倾向于函数式编程，view = f(state)，鼓励使用不可变数据，当 state 发生改变时，react 默认会使用浅比较来对比状态的差异，更具对比结果决定是否更新视图。