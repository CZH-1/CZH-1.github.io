---
title: "JS 手写函数"
date: 2024-02-29 12:22:54
categories:
- JavaScript
tags:
- JavaScript
toc: true # 是否显示目录
---
> this
<!-- more -->

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
```js
// new
function newSelf(fn, args) {
  const constructor = Object.create(fn.prototype)
  const obj = fn.apply(constructor, args)
  return typeof obj === 'object' && obj !== null ? obj : constructor
}
```
```js
// 判断类型
function dataType(data){
  return Object.prototype.toString.call(data).slice(8,-1).toLowerCase()
}
console.log(dataType(new Date()));
```

