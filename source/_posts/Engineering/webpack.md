---
title: "Webpack 打包流程"
date: 2024-03-05 17:14:08
categories:
- 工程化
tags:
- 工程化
toc: true # 是否显示目录
---

> Webpack 打包流程

<!-- more -->

## 大致流程
整个实现过程大致分为以下步骤：

1. 搭建结构，读取配置参数
2. 用配置参数对象初始化 `Compiler` 对象
3. 挂载配置文件中的插件
4. 执行 `Compiler` 对象的 `run` 方法开始执行编译
5. 根据配置文件中的 `entry` 配置项找到所有的入口
6. 从入口文件出发，调用配置的 `loader` 规则，对各模块进行编译
7. 找出此模块所依赖的模块，再对依赖模块进行编译
8. 等所有模块都编译完成后，根据模块之间的依赖关系，组装代码块 `chunk`
9. 把各个代码块 `chunk` 转换成一个一个文件加入到输出列表
10. 确定好输出内容之后，根据配置的输出路径和文件名，将文件内容写入到文件系统


## 参考
* [webpack官网](https://www.webpackjs.com/concepts/plugins/#anatomy)
* [二十张图片彻底讲明白Webpack设计理念，以看懂为目的](https://juejin.cn/post/7170852747749621791)
* [【webpack系列】从核心概念到上手配置](https://www.developers.pub/wiki/1065322/1215712)
* [webpack配置完全指南](https://juejin.cn/post/7205527600700571709)