---
title: "Vue组件通信的8种方式"
date: 2022-03-12 12:22:54
categories:
- Vue
tags:
- Vue
toc: true # 是否显示目录
---

> * props 和 $emit
> * $children 和 $parent
> * ref
> * provide 和 inject
> * eventBus
> * $attrs 和 $listeners
> * Vuex
> * localStorage 和 sessionStorage

<!-- more -->
* 父子关系的组件数据传递选择 `props` 与 `$emit` 进行传递，也可选择 `ref`
* 兄弟关系的组件数据传递可选择 `$bus`，其次可以选择 `$parent` 进行传递
* 祖先与后代组件数据传递可选择 `attrs` 与 `listeners` 或者 `Provide` 与 `Inject`
* 复杂关系的组件数据传递可以通过 `vuex` 存放共享的变量
## 参考
* [Vue组件通信](https://juejin.cn/post/6844903887162310669#comment)
* [Vue通信方式](https://www.developers.pub/wiki/1065322/1065399)
