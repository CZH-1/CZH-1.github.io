---
title: "节流 & 防抖"
date: 2022-03-12 12:22:54
categories:
- JavaScript
tags:
- JavaScript
toc: true # 是否显示目录
---

> 节流 & 防抖 

<!-- more -->

## 节流和防抖的区别

节流：事件触发后，立即执行且n秒内只执行一次

防抖：事件触发后，等待n秒执行，n秒内再次触发，则取消上次，执行最后一次

## 节流和防抖的使用场景

节流：表单重复提交，按钮高频点击，滚动加载

防抖：搜索框自动搜索，输入框校验，窗口大小改变重新渲染

## 节流和防抖的实现

```bash
# 节流函数 一个函数执行一次后，只有大于设定的执行周期才会执行第二次。有个需要频繁触发的函数，出于优化性能的角度，在规定时间内，只让函数触发的第一次生效，后面的不生效。
# @param fn要被节流的函数
# @param delay规定的时间
function throttle(delay){
  let lastTime = 0
  return function (){
    let nowTime = Date.now()
    if(nowTime - lastTime > delay){
      # 修正this指向问题
      fn.call(this)
      lastTime = nowTime
    }
  }
}

```
```bash
# 防抖函数 一个需要频繁触发的函数，在规定时间内，只让最后一次生效，前面的不生效
# @param fn要被防抖的函数
# @param delay规定的时间
function debounce(delay){
  let timer = null
  return function (){
    # 清除上一次的演示器
    clearTimeout(timer)
    timer = setTimeout(()=>{
      # 修正this指向问题
      fn.apply(this)
    },delay)
  }
}

document.getElementById('btn').onclick = debounce(function () {
    console.log('按钮被点击了' + Date.now());
}, 1000);

```

https://juejin.cn/post/7204707115062411320#heading-54



