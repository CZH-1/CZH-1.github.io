---
title: "this"
date: 2022-03-12 12:22:54
categories:
- JavaScript
tags:
- JavaScript
toc: true # 是否显示目录
---
> this
<!-- more -->

# 箭头函数和普通函数有什么区别
```bash
var obj2 = {
  a: 2,
  foo1: function () {
    console.log(this,this.a) // 2
  },
  foo2: function () {
    setTimeout(function () {
      console.log(this) // window
      console.log(this.a) // 3
    }, 0)
  }
}
var a = 3
console.log(this);

obj2.foo1()
obj2.foo2() 

var id = 'aa'
console.log(this);
console.log(global);
function ab(){
  let id = 'b'
  console.log(this === global)
  return this.id
}
console.log(ab())
```

https://juejin.cn/post/7204707115062411320#heading-9
https://juejin.cn/post/7146973901166215176#heading-8