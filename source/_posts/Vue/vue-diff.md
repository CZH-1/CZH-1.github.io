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
> * vuex
> * vue-router 3种模式
> * 虚拟dom
> * 了解 Vue diff算法
> * vue 底层代码
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


## vuex

> vuex的出现是为了解决组件间的通信问题,如果某个操作或者数据不涉及到公共操作,只是单一组件操作,不要把这些状态值或者function存储到vuex中,因为vuex会把自身挂载到所有组件上,不管当前组件是否用到里面的东西,因此这事实上肯定增加了性能的损耗的.

vuex中，有默认的五种基本的对象：

* state：定义了应用状态的数据结构，可以在这里设置默认的初始状态
* getters：对数据获取之前的再次编译，可以理解为state的计算属性。
* mutations：修改状态，并且是同步的。这个和我们组件中的自定义事件类似。
* actions：用于提交 mutation，而不是直接变更状态，可以包含任意异步操作。
* modules：允许将单一的 Store 拆分为多个 store 且同时保存在单一的状态树中。
* mapGetters 辅助函数

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

## 简易版vuex
vue.js 2.6 新增了Observable API， 通过这个api，可以应对一些简单的跨组件数据共享。像是简易版vuex

## 虚拟DOM
### 什么是虚拟DOM
虚拟DOM简而言之就是，用JS去按照DOM结构来实现的树形结构对象，你也可以叫做DOM对象
Virtual DOM是对真实DOM的抽象,本质上是树形结构JavaScript对象,这个对象就是更加轻量级的对DOM的描述.
### 虚拟DOM的优点
* 通过diff算法来对比dom新老节点，更新虚拟DOM，减少对真实DOM的操作开销，提高性能。
* 跨平台 抽象了原本的渲染过程实现了跨平台，不仅仅局限于浏览器的 DOM，可以是安卓和 IOS 的原生组件，可以是近期很火热的小程序，也可以是各种GUI。以及使用Node.js（没有DOM）实现SSR(服务端渲染)。
### 虚拟DOM对象
```bash
# 真实DOM
<div id="app">
  <p class="text">hello world!!!</p>
</div>

# 虚拟DOM
{
  tag: 'div',
  props: {
    id: 'app'
  },
  chidren: [
    {
      tag: 'p',
      props: {
        className: 'text'
      },
      chidren: [
        'hello world!!!'
      ]
    }
  ]
}
```

### 如何转换为虚拟DOM
 React.createElement，以及 Vue 中的 render 方法中的 createElement，另外 React 是通过 babel 将 jsx 转换为 h 函数渲染的形式，而 Vue 是使用 vue-loader 将模版转为 h 函数渲染的形式（也可以通过 babel-plugin-transform-vue-jsx 插件在 vue 中使用 jsx，本质还是转换为 h 函数渲染形式）。
```bash
已react为例，使用 babel 将一段 jsx 代码，转换为一段 js 代码：
function getVDOM() {
  return (
    <div id="app">
      <p className="text">hello world!!!</p>
    </div>
  )
}
function getVDOM() {
  return h('div', {
    id: 'app'
  },h('p', {
    lassName: 'text'
  },'hello world!!!'))
}

可以看到，最终 HTML 代码会被转译成 h 函数的渲染形式。h 函数接受是三个参数，分别代表是 DOM 元素的标签名、属性、子节点，最终返回一个虚拟 DOM 的对象。
function h(tag, props, ...children) {
  return {
    tag,
    props: props || {},
    children: children.flat()
  }
}
```

### 渲染虚拟DOM

浏览器环境下如何渲染虚拟 DOM。

```bash
function render(vdom) {
  // 如果是字符串或者数字，创建一个文本节点
  if (typeof vdom === 'string' || typeof vdom === 'number') {
    return document.createTextNode(vdom)
  }
  const { tag, props, children } = vdom
  // 创建真实DOM
  const element = document.createElement(tag)
  // 设置属性
  setProps(element, props)
  // 遍历子节点，并获取创建真实DOM，插入到当前节点
  children
    .map(render)
    .forEach(element.appendChild.bind(element))

  // 虚拟 DOM 中缓存真实 DOM 节点
  vdom.dom = element

  // 返回 DOM 节点
  return element
}

function setProps (element, props) {
  Object.entries(props).forEach(([key, value]) => {
    setProp(element, key, value)
  })
}

function setProp (element, key, vlaue) {
  element.setAttribute(
    // className使用class代替
    key === 'className' ? 'class' : key,
    vlaue
  )
}

```

将虚拟 DOM 渲染成真实 DOM 后，只需要插入到对应的根节点即可。

```bash
const vdom = <div>hello world!!!</div> // h('div', {}, 'hello world!!!')
const app = document.getElementById('app')
const ele = render(vdom)
app.appendChild(ele)
```

当然在现代化的框架中，一般会有一个组件文件专门用来构造虚拟 DOM，我们模仿 React 使用 class 的方式编写组件，然后渲染到页面中。
```bash
class Component {
  vdom = null // 组件的虚拟DOM表示
  $el  = null // 虚拟DOM生成的真实节点

  state = {
    text: 'Initialize the Component'
  }

  render() {
    const { text } = this.state
    return (
      <div>{ text }</div>
    )
  }
}

function createElement (app, component) {
  const vdom = component.render()
  component.vdom = vdom
  component.$el = render(vdom) // 将虚拟 DOM 转换为真实 DOM
  app.appendChild(component.$el)
}

const app = document.getElementById('app')
const component = new Component
createElement(app, component)
```
## diff算法

diff 算法，顾名思义，就是比对新老 VDOM 的变化，然后将变化的部分更新到视图上。对应到代码上，就是一个 diff 函数，返回一个 patches （补丁）。

[参考](https://juejin.cn/post/6844903887162310669#comment)

[虚拟DOM原理的理解](https://juejin.cn/post/6844903902429577229#heading-6)

[虚拟DOM和DOM-diff](https://juejin.cn/post/6844903806132568072#heading-0)

[虚拟DOM到底是什么](https://mp.weixin.qq.com/s/oAlVmZ4Hbt2VhOwFEkNEhw)

[diff算法详解](https://juejin.cn/post/7204844328111374391)


## Vue底层架构 core watcher vnode diff算法
[Vue底层架构](https://www.cnblogs.com/rubyxie/articles/10950050.html)

### 数据监听
[参考](https://juejin.cn/post/6844903918753808398#heading-18)

### $set
Vue 怎么用 vm.$set() 解决对象新增属性不能响应的问题 ？

受现代 JavaScript 的限制 ，Vue 无法检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化，所以属性必须在 data 对象上存在才能让 Vue 将它转换为响应式的。但是 Vue 提供了 Vue.set (object, propertyName, value) / vm.$set (object, propertyName, value)  来实现为对象添加响应式属性，那框架本身是如何实现的呢？

```bash
# 源码
export function set (target: Array<any> | Object, key: any, val: any): any {
  // target 为数组  
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    // 修改数组的长度, 避免索引>数组长度导致splcie()执行有误
    target.length = Math.max(target.length, key)
    // 利用数组的splice变异方法触发响应式  
    target.splice(key, 1, val)
    return val
  }
  // key 已经存在，直接修改属性值  
  if (key in target && !(key in Object.prototype)) {
    target[key] = val
    return val
  }
  const ob = (target: any).__ob__
  // target 本身就不是响应式数据, 直接赋值
  if (!ob) {
    target[key] = val
    return val
  }
  // 对属性进行响应式处理
  defineReactive(ob.value, key, val)
  ob.dep.notify()
  return val
}
```
我们阅读以上源码可知，vm.$set 的实现原理是：
* 如果目标是数组，直接使用数组的 splice 方法触发相应式；
* 如果目标是对象，会先判读属性是否存在、对象是否是响应式，最终如果要对属性进行响应式处理，则是通过调用   defineReactive 方法进行响应式处理（ defineReactive 方法就是  Vue 在初始化对象时，给对象属性采用 Object.defineProperty 动态添加 getter 和 setter 的功能所调用的方法）
