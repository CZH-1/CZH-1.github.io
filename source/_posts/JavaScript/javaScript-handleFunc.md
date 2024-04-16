---
title: "JS 手写函数"
date: 2024-02-29 12:22:54
categories:
- JavaScript
tags:
- JavaScript
toc: true # 是否显示目录
---
* 深拷贝
* new
* 判断类型
* 发布订阅模式
<!-- more -->

## 深拷贝
```js
// 深拷贝
function deepClone(source) {
  if (!source && typeof source !== 'object') {
    return source
  }
  const target = Array.isArray(source) ? [] : {}
  Object.keys(source).forEach(key => {
    if (source[key] && typeof key === 'object') {
      target[key] = deepClone(source[key])
    } else {
      target[key] = source[key]
    }
  })
  return target
}

// 深拷贝
function deepClone(target,hash = new weakMap){
  if(target === null && target!=='object'){
    return target
  }
  if(hash.get(target)) return hash.get(target)
  const newObj = Array.isArray(target)? []:{}
  hash.set(target,newObj)
  for(let key in target){
    if(target.hasOwnProperty(key)){
      if(target[key] !== null && target[key] === 'object'){
        newObj[key] = deepClone(target[key])
      } else {
        newObj[key] = target[key]
      }
    }
  }
  return newObj
}

function deepClone(obj, hash = new WeakMap()) {
  if (obj === null) return obj; // 如果是null或者undefined我就不进行拷贝操作
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);
  // 可能是对象或者普通的值  如果是函数的话是不需要深拷贝
  if (typeof obj !== "object") return obj;
  // 是对象的话就要进行深拷贝
  if (hash.get(obj)) return hash.get(obj);
  let cloneObj = new obj.constructor();
  // 找到的是所属类原型上的constructor,而原型上的 constructor指向的是当前类本身
  hash.set(obj, cloneObj);
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      // 实现一个递归拷贝
      cloneObj[key] = deepClone(obj[key], hash);
    }
  }
  return cloneObj;
}
```
## new 
```js
// new
function newSelf(fn, args) {
  const constructor = Object.create(fn.prototype)
  const obj = fn.apply(constructor, args)
  return typeof obj === 'object' && obj !== null ? obj : constructor
}
```
## 判断类型
```js
// 判断类型
function dataType(data){
  return Object.prototype.toString.call(data).slice(8,-1).toLowerCase()
}
console.log(dataType(new Date()));
```
## 手动实现发布订阅模式(基础版)
实现目标：使用 $on 订阅事件，使用 $emit 发布事件。
主要思路：  
* 创建一个缓存列表对象，存放订阅的事件名和回调
* on 方法用来把回调函数都加到缓存列表中（订阅者注册事件到调度中心）
* emit方法根据事件名去逐个执行对应缓存列表中的函数（发布者发布事件到调度中心）
* off 取消订阅
```js

class EventBus {
  constructor() {
    // 缓存列表，用来存放注册的事件与回调
    this.cache = {};
  }

  // 订阅事件
  on(name, cb) {
    // 如果当前事件没有订阅过，就给事件创建一个队列
    if (!this.cache[name]) {
      this.cache[name] = []; //由于一个事件可能注册多个回调函数，所以使用数组来存储事件队列
    }
    this.cache[name].push(cb); 
  }

  // 触发事件
  emit(name, ...args) {
    // 检查目标事件是否有监听函数队列
    if (this.cache[name]) {
      // 逐个调用队列里的回调函数
      this.cache[name].forEach((callback) => {
        callback(...args);
      });
    }
  }
  off(name){
    delete this.cache[name]
  }
}

// 测试
let eventBus = new EventBus();
// 订阅事件
eventBus.on("teacherName1", (pos, state) => {
  console.log(`订阅者小陈老师,小明同学当前在${pos},心情状态是${state}`);
});
eventBus.on("teacherName1", (pos, state) => {
  console.log(`订阅者小陈老师,小明同学当前在${pos},心情状态是${state}`);
});
eventBus.on("teacherName2", (pos, state) => {
  console.log(`订阅者小李老师,小明同学当前在${pos},心情状态是${state}`);
});
// 发布事件
eventBus.emit("teacherName1", "教室", "伤心");
eventBus.emit("teacherName2", "操场", "开心");
// 取消
eventBus.off("teacherName2");
```

