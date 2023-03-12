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

## js 创建对象的方式

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