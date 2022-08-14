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

## JS垃圾回收机制（内存泄露）

[「硬核JS」你真的了解垃圾回收机制吗](https://juejin.cn/post/6981588276356317214#heading-6)
### GC是什么

GC 即 Garbage Collection （垃圾回收），程序工作过程中会产生很多 垃圾，这些垃圾是程序不用的内存或者是之前用过了，以后不会再用的内存空间，而 GC 就是负责回收垃圾的，因为他工作在引擎内部，所以对于我们前端来说，GC 过程是相对比较无感的，这一套引擎执行而对我们又相对无感的操作也就是常说的 垃圾回收机制 了

### 垃圾产生&为何回收
JavaScript 是在创建变量（对象，字符串等）时自动进行了分配内存，并且在不使用它们时“自动”释放。 释放的过程称为垃圾回收。
常见的内存泄漏：
* 意外声明的全局变量
* 被遗忘的定时器
* 使用不当的闭包
* 未清理的 DOM 引用

### 垃圾回收策略

1. 标记清除算法

* 垃圾收集器在运行时会给内存中的所有变量都加上一个标记，假设内存中所有对象都是垃圾，全标记为0
* 然后从各个根对象开始遍历，把不是垃圾的节点改成1
* 清理所有标记为0的垃圾，销毁并回收它们所占用的内存空间
* 最后，把所有内存中对象标记修改为0，等待下一轮垃圾回收

2. 引用计数算法

它的策略是跟踪记录每个变量值被使用的次数

* 当声明了一个变量并且将一个引用类型赋值给该变量的时候这个值的引用次数就为 1
* 如果同一个值又被赋给另一个变量，那么引用数加 1
* 如果该变量的值被其他的值覆盖了，则引用次数减 1
* 当这个值的引用次数变为 0 的时候，说明没有变量在使用，这个值没法被访问了，回收空间，垃圾回收器会在运行的时候清理掉引用次数为 0 的值占用的内存


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