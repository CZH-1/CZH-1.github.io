---
title: "vue-router"
date: 2022-03-12 12:22:54
categories:
- Vue
tags:
- Vue
toc: true # 是否显示目录
---

> * 路由模式 
> * 路由守卫

<!-- more -->
## 路由模式 

vue-router 有 3 种路由模式：hash、history、abstract：

* hash: 使用 URL hash 值来作路由。支持所有浏览器，包括不支持 HTML5 History Api 的浏览器;
* history: 依赖 HTML5 History API 和服务器配置(常用);
* abstract: 支持所有 JavaScript 运行环境，如 Node.js 服务器端。如果发现没有浏览器的 API，路由会自动强制进入这个模式;

详细：https://juejin.cn/post/7127143415879303204

## 路由守卫
### 全局守卫：
```bash
const router = createRouter({ ... })
router.beforeEach((to, from) => {
  // ...
  // 返回 false 以取消导航
  return false
})
```
### 路由独享守卫：
```bash
const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: (to, from) => {
      // reject the navigation
      return false
    },
  },
]
```
### 组件内的守卫：
```bash
const UserDetails = {
  template: `...`,
  beforeRouteEnter(to, from) {
    // 在渲染该组件的对应路由被验证前调用
  },
  beforeRouteUpdate(to, from) {
    // 在当前路由改变，但是该组件被复用时调用
  },
  beforeRouteLeave(to, from) {
    // 在导航离开渲染该组件的对应路由时调用
  },
}
```

* vue-router中保护路由的方法叫做路由守卫，主要用来通过跳转或取消的方式守卫导航。
* 路由守卫有三个级别：全局、路由独享、组件级。影响范围由大到小，例如全局的router.beforeEach()，可以注册一个全局前置守卫，每次路由导航都会经过这个守卫，因此在其内部可以加入控制逻辑决定用户是否可以导航到目标路由；在路由注册的时候可以加入单路由独享的守卫，例如beforeEnter，守卫只在进入路由时触发，因此只会影响这个路由，控制更精确；我们还可以为路由组件添加守卫配置，例如beforeRouteEnter，会在渲染该组件的对应路由被验证前调用，控制的范围更精确了。
* 用户的任何导航行为都会走navigate方法，内部有个guards队列按顺序执行用户注册的守卫钩子函数，如果没有通过验证逻辑则会取消原有的导航。

> 1. 全局前置/钩子：beforeEach、beforeResolve、afterEach
> 2. 路由独享的守卫：beforeEnter
> 3. 组件内的守卫：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave

[参考](https://juejin.cn/post/7204844328111374391)

[参考](https://juejin.cn/post/6844903918753808398#heading-18)