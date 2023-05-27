---
title: "Vue项目性能优化"
date: 2022-03-12 12:22:54
categories:
- 性能优化
tags:
- 性能优化
toc: true # 是否显示目录
---

> * Vue项目性能优化 

<!-- more -->

## 代码层面
* 合理使用 v-for 以及 v-show
* v-for 增加key 使diff算法更快速
* 不需要响应式处理的数据可以通过`Object.freeze`处理，或者直接通过`this.xxx = xxx`的方式进行定义
* 合理定义变量，事件销毁回收
* 长列表性能优化，例如虚拟渲染，滚动加载等等
* 图片懒加载
* 路由懒加载 https://blog.csdn.net/mynewdays/article/details/124446128
* 第三方插件按需引入
* 精简优化代码，例如组件化等
* 减少回流重绘
* 防抖节流


## webpack层面
* webpack对图片压缩
* 模板预编译
* 减少 ES6 转为 ES5 的冗余代码  babel-plugin-transform-runtime减少
* 提取公共代码
* 提取组件的 CSS
* 优化 SourceMap
* 构建结果输出分析
* Vue 项目的编译优化
* 代码分离 splitChunksPlugin 将代码分离到不同的bundle中，之后我们可以按需加载，或者并行加载这些文件
* 代码压缩，Html,js和css mode:production，使用的是terser-webpack-plugin css-minimizer-webpack-plugin HtmlwebpackPlugin
* Tree Shaking 去除未引用代码

## 基础的 Web 技术的优化
* 开启 gzip 压缩
* 浏览器缓存
* CDN 的使用
* 使用 Chrome Performance 查找性能瓶颈

## 其他
* 尽量使用图标，不用图片

## 参考
[Vue性能优化](https://juejin.cn/post/6844903918753808398#heading-18)

[当面试官问我前端可以做的性能优化有哪些](https://juejin.cn/post/7194400984490049573)