---
title: "new"
date: 2022-03-12 12:22:54
categories:
- JavaScript
tags:
- JavaScript
toc: true # 是否显示目录
---

> new

<!-- more -->

new一个对象，到底发生什么？

1）创建一个对象，该对象的原型指向构造函数的原型
2）调用该构造函数，构造函数的this指向新生成的对象
3）判断构造函数是否有返回值，如果有返回值且返回值是一个对象或一个方法，则返回该值；否则返回新生成的对象

构造函数有返回值的案例:
```bash
function Dog(name) {
  this.name = name;
  return { test: 1 };
}
let obj = new Dog("ming");
console.log(obj); // {test:1} 
```
手写 new:

```bash
function selfNew(fn, ...args) {
  // 创建一个instance对象，该对象的原型是fn.prototype
  let instance = Object.create(fn.prototype);
  // 调用构造函数，使用apply，将this指向新生成的对象
  let res = fn.apply(instance, args);
  // 如果fn函数有返回值，并且返回值是一个对象或方法，则返回该对象，否则返回新生成的instance对象
  return typeof res === "object" || typeof res === "function" ? res : instance;
}
```

## 参考
* [「历时8个月」10万字前端知识体系总结（基础知识篇）🔥](https://juejin.cn/post/7146973901166215176)


