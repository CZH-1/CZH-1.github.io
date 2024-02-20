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
```bash
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
```bash
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


## 参考
* [V8 引擎垃圾回收与内存分配](https://juejin.cn/post/6909239354418266119#comment)


