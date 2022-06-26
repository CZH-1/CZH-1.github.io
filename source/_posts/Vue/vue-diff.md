---
title: "了解 Vue diff算法"
date: 2022-03-12 12:22:54
categories:
- Vue
tags:
- Vue
toc: true # 是否显示目录
---
> * 生命周期
> * 了解 Vue diff算法
> * 虚拟dom
> * vuex
> * vue-router 3种模式
> * vue3 及 与 vue2 的区别


<!-- more -->

## vue 生命周期
vue 的生命周期钩子核心实现是利用发布订阅模式，先把用户传入的生命周期钩子订阅好，内部使用数组方式存储，然后再创建实例过程中会一次执行对应的钩子方式(发布)

* beforeCreate: 是 new Vue() 之后触发的第一个钩子，当前阶段 data、methods、computed 以及 watch 上的数据和方法都不能访问。
* created: 可以使用数据，更改数据，无法与Dom 进行交互。在实例创建完成后发生，当前阶段已经完成了数据观测，也就是可以使用数据，更改数据，在这里更改数据不会触发 updated 函数。可以做一些初始数据的获取，在当前阶段无法与 Dom 进行交互，如果非要想，可以通过 vm.$nextTick 来访问 Dom。
* beforeMount: 虚拟dom已生成，真实dom未挂载。发生在挂载之前，在这之前 template 模板已导入渲染函数编译。而当前阶段虚拟 Dom 已经创建完成，即将开始渲染。在此时也可以对数据进行更改，不会触发 updated。
* mounted: 真实Dom已挂载，数据完成双向绑定，可以使用 $refs 对Dom 操作。在挂载完成后发生，在当前阶段，真实的 Dom 挂载完毕，数据完成双向绑定，可以访问到 Dom 节点，使用 $refs 属性对 Dom 进行操作。
* beforeUpdate: 发生在更新之前，也就是响应式数据发生更新，虚拟 dom 重新渲染之前被触发，你可以在当前阶段进行更改数据，不会造成重渲染。
* updated: 发生在更新完成之后，当前阶段组件 Dom 已完成更新。要注意的是避免在此期间更改数据，因为这可能会导致无限循环的更新。
* beforeDestroy: 发生在实例销毁之前，在当前阶段实例完全可以被使用，我们可以在这时进行善后收尾工作，比如清除计时器。
* destroyed:  发生在实例销毁之后，这个时候只剩下了 dom 空壳。组件已被拆解，数据绑定被卸除，监听被移出，子实例也统统被销毁。

## vue-router 有几种模式
vue-router 有 3 种路由模式：hash、history、abstract：

* hash: 使用 URL hash 值来作路由。支持所有浏览器，包括不支持 HTML5 History Api 的浏览器;
* history: 依赖 HTML5 History API 和服务器配置(常用);
* abstract: 支持所有 JavaScript 运行环境，如 Node.js 服务器端。如果发现没有浏览器的 API，路由会自动强制进入这个模式;


## vuex

> vuex的出现是为了解决组件间的通信问题,如果某个操作或者数据不涉及到公共操作,只是单一组件操作,不要把这些状态值或者function存储到vuex中,因为vuex会把自身挂载到所有组件上,不管当前组件是否用到里面的东西,因此这事实上肯定增加了性能的损耗的.

vuex中，有默认的五种基本的对象：

* state：存储状态（变量）
* getters：对数据获取之前的再次编译，可以理解为state的计算属性。
* mutations：修改状态，并且是同步的。这个和我们组件中的自定义事件类似。
* actions：异步操作。
* modules：store的子模块

项目的src文件夹如下：
```bash
│  App.vue
│  main.js
│
├─assets
│      logo.png
│
├─components
│      HelloWorld.vue
│
├─router
│      index.js
│
└─store
      modules
            user.js
            app.js
            ...
      getters.js
      index.js

```
index.js 文件内容如下：

```bash
import Vue from 'vue'
import Vuex from 'vuex'
import getters from './getters'

Vue.use(Vuex)
const modulesFiles = require.context('./modules', true, /\.js$/)

// you do not need `import app from './modules/app'`
// it will auto require all vuex module from modules file
const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  // set './app.js' => 'app'
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default
  return modules
}, {})

const store = new Vuex.Store({
  modules,
  getters
})

export default store
```
getters.js 文件内容如下：

```bash
const getters = {
  userName: state => state.user.userName,
  userId: state => state.user.userId,
  sessionId: state => state.app.sessionId,
  ...
}
export default getters

```
1. 存储数据

```bash
# 调用存储
# app 为 modules 文件名，setSessionId 为 actions 中方法名
this.$store.dispatch('app/setSessionId', data.sessionId)
```
```bash
# app.js
const state = {
  sessionId: null,
}

const mutations = {
  SET_SESSIONID: (state, sessionId) => {
    state.sessionId = sessionId
  },
}

const actions = {
  setSessionId({ commit }, sessionId) {
    commit('SET_SESSIONID', sessionId)
  },
}

export default {
  namespaced: true,
  state,
  mutations,
  actions
}
```
2. 调用数据
```bash
computed: {
  ...mapGetters(['userId', 'sessionId']),
}
```



[参考](https://juejin.cn/post/6844903887162310669#comment)
