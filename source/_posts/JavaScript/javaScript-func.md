---
title: "JS 手写函数"
date: 2022-03-12 12:22:54
categories:
- JavaScript
tags:
- JavaScript
toc: true # 是否显示目录
---
> this
<!-- more -->

```bash
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
```
```bash
// new
function newSelf(fn, args) {
  const constructor = Object.create(fn.prototype)
  const obj = fn.apply(constructor, args)
  return typeof obj === 'object' && obj !== null ? obj : constructor
}
```
```bash
// 判断类型
function dataType(data){
  return Object.prototype.toString.call(data).slice(8,-1).toLowerCase()
}
console.log(dataType(new Date()));
```
```bash
// 快速排序
// 找一个基点，小于放左边，大于放右边
function quickSort(arr,startIndex = 0) {
  // console.log(startIndex)
  if (arr.length <= 1) return arr
  const startNum = arr.splice(startIndex, 1)[0];
    console.log(startNum);
  const right = [],
    left= []
    
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] < startNum) {
      left.push(arr[i]);
    } else {
      right.push(arr[i]);
    }
  }
  return [...quickSort(left), startNum, ...quickSort(right)];
}
const arr = [6, 4, 7, 5, 1, 2, 9, 8]
console.log(arr)
console.log(quickSort(arr))
```

```bash
// 冒泡排序
// 比较相邻的两个数，若大于交换位置，每一轮找出一个最大值
function bubbleSort(arr) {
  // 缓存数组长度
  const len = arr.length;
  // 外层循环用于控制从头到尾的比较+交换到底有多少轮
  for (let i = 0; i < len; i++) {
    // 内层循环用于完成每一轮遍历过程中的重复比较+交换
    for (let j = 0; j < len - 1; j++) {
      // 若相邻元素前面的数比后面的大
      if (arr[j] > arr[j + 1]) {
        // 交换两者
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
      }
    }
  }
  // 返回数组
  return arr;
}
console.log(bubbleSort([3, 6, 2, 4, 1]));
```
// 选择排序
// 找出最小的值

