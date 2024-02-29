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

> 很多人认为虚拟 DOM 最大的优势是 diff 算法，减少 JavaScript 操作真实 DOM 的带来的性能消耗。虽然这一个虚拟 DOM 带来的一个优势，但并不是全部。虚拟 DOM 最大的优势在于抽象了原本的渲染过程，实现了跨平台的能力，而不仅仅局限于浏览器的 DOM，可以是安卓和 IOS 的原生组件，可以是近期很火热的小程序，也可以是各种GUI

## diff算法

> diff 算法，顾名思义，就是比对新老 VDOM 的变化，然后将变化的部分更新到视图上。对应到代码上，就是一个 diff 函数，返回一个 patches （补丁）。

当数据发生改变时，set方法会调用Dep.notify通知所有订阅者Watcher，订阅者就会调用patch给真实的DOM打补丁，更新相应的视图

```bash
function patch(oldVnode, vnode, hydrating, removeOnly) {
    if (isUndef(vnode)) { // 没有新节点，直接执行destory钩子函数
        if (isDef(oldVnode)) invokeDestroyHook(oldVnode)
        return
    }

    let isInitialPatch = false
    const insertedVnodeQueue = []

    if (isUndef(oldVnode)) {
        isInitialPatch = true
        createElm(vnode, insertedVnodeQueue) // 没有旧节点，直接用新节点生成dom元素
    } else {
        const isRealElement = isDef(oldVnode.nodeType)
        if (!isRealElement && sameVnode(oldVnode, vnode)) {
            // 判断旧节点和新节点自身一样，一致执行patchVnode
            patchVnode(oldVnode, vnode, insertedVnodeQueue, null, null, removeOnly)
        } else {
            // 否则直接销毁及旧节点，根据新节点生成dom元素
            if (isRealElement) {

                if (oldVnode.nodeType === 1 && oldVnode.hasAttribute(SSR_ATTR)) {
                    oldVnode.removeAttribute(SSR_ATTR)
                    hydrating = true
                }
                if (isTrue(hydrating)) {
                    if (hydrate(oldVnode, vnode, insertedVnodeQueue)) {
                        invokeInsertHook(vnode, insertedVnodeQueue, true)
                        return oldVnode
                    }
                }
                oldVnode = emptyNodeAt(oldVnode)
            }
            return vnode.elm
        }
    }
}
```

patch函数前两个参数位为oldVnode 和 Vnode ，分别代表新的节点和之前的旧节点，主要做了四个判断：

* 没有新节点，直接触发旧节点的destory钩子
* 没有旧节点，说明是页面刚开始初始化的时候，此时，根本不需要比较了，直接全是新建，所以只调用 createElm
* 旧节点和新节点自身一样，通过 sameVnode 判断节点是否一样，一样时，直接调用 patchVnode 去处理这两个节点
* 旧节点和新节点自身不一样，当两个节点不一样的时候，直接创建新节点，删除旧节点

下面主要讲的是patchVnode部分:
```bash
function patchVnode (oldVnode, vnode, insertedVnodeQueue, removeOnly) {
    // 如果新旧节点一致，什么都不做
    if (oldVnode === vnode) {
      return
    }

    // 让vnode.el引用到现在的真实dom，当el修改时，vnode.el会同步变化
    const elm = vnode.elm = oldVnode.elm

    // 异步占位符
    if (isTrue(oldVnode.isAsyncPlaceholder)) {
      if (isDef(vnode.asyncFactory.resolved)) {
        hydrate(oldVnode.elm, vnode, insertedVnodeQueue)
      } else {
        vnode.isAsyncPlaceholder = true
      }
      return
    }
    // 如果新旧都是静态节点，并且具有相同的key
    // 当vnode是克隆节点或是v-once指令控制的节点时，只需要把oldVnode.elm和oldVnode.child都复制到vnode上
    // 也不用再有其他操作
    if (isTrue(vnode.isStatic) &&
      isTrue(oldVnode.isStatic) &&
      vnode.key === oldVnode.key &&
      (isTrue(vnode.isCloned) || isTrue(vnode.isOnce))
    ) {
      vnode.componentInstance = oldVnode.componentInstance
      return
    }

    let i
    const data = vnode.data
    if (isDef(data) && isDef(i = data.hook) && isDef(i = i.prepatch)) {
      i(oldVnode, vnode)
    }

    const oldCh = oldVnode.children
    const ch = vnode.children
    if (isDef(data) && isPatchable(vnode)) {
      for (i = 0; i < cbs.update.length; ++i) cbs.update[i](oldVnode, vnode)
      if (isDef(i = data.hook) && isDef(i = i.update)) i(oldVnode, vnode)
    }
    // 如果vnode不是文本节点或者注释节点
    if (isUndef(vnode.text)) {
      // 并且都有子节点
      if (isDef(oldCh) && isDef(ch)) {
        // 并且子节点不完全一致，则调用updateChildren
        if (oldCh !== ch) updateChildren(elm, oldCh, ch, insertedVnodeQueue, removeOnly)

        // 如果只有新的vnode有子节点
      } else if (isDef(ch)) {
        if (isDef(oldVnode.text)) nodeOps.setTextContent(elm, '')
        // elm已经引用了老的dom节点，在老的dom节点上添加子节点
        addVnodes(elm, null, ch, 0, ch.length - 1, insertedVnodeQueue)

        // 如果新vnode没有子节点，而vnode有子节点，直接删除老的oldCh
      } else if (isDef(oldCh)) {
        removeVnodes(elm, oldCh, 0, oldCh.length - 1)

        // 如果老节点是文本节点
      } else if (isDef(oldVnode.text)) {
        nodeOps.setTextContent(elm, '')
      }

      // 如果新vnode和老vnode是文本节点或注释节点
      // 但是vnode.text != oldVnode.text时，只需要更新vnode.elm的文本内容就可以
    } else if (oldVnode.text !== vnode.text) {
      nodeOps.setTextContent(elm, vnode.text)
    }
    if (isDef(data)) {
      if (isDef(i = data.hook) && isDef(i = i.postpatch)) i(oldVnode, vnode)
    }
  }
```
patchVnode主要做了几个判断：

* 新节点是否是文本节点，如果是，则直接更新dom的文本内容为新节点的文本内容
* 新节点和旧节点如果都有子节点，则处理比较更新子节点
* 只有新节点有子节点，旧节点没有，那么不用比较了，所有节点都是全新的，所以直接全部新建就好了，新建是指创建出所有新DOM，并且添加进父节点
* 只有旧节点有子节点而新节点没有，说明更新后的页面，旧节点全部都不见了，那么要做的，就是把所有的旧节点删除，也就是直接把DOM 删除

子节点不完全一致，则调用updateChildren:
```bash
function updateChildren (parentElm, oldCh, newCh, insertedVnodeQueue, removeOnly) {
    let oldStartIdx = 0 // 旧头索引
    let newStartIdx = 0 // 新头索引
    let oldEndIdx = oldCh.length - 1 // 旧尾索引
    let newEndIdx = newCh.length - 1 // 新尾索引
    let oldStartVnode = oldCh[0] // oldVnode的第一个child
    let oldEndVnode = oldCh[oldEndIdx] // oldVnode的最后一个child
    let newStartVnode = newCh[0] // newVnode的第一个child
    let newEndVnode = newCh[newEndIdx] // newVnode的最后一个child
    let oldKeyToIdx, idxInOld, vnodeToMove, refElm

    // removeOnly is a special flag used only by <transition-group>
    // to ensure removed elements stay in correct relative positions
    // during leaving transitions
    const canMove = !removeOnly

    // 如果oldStartVnode和oldEndVnode重合，并且新的也都重合了，证明diff完了，循环结束
    while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
      // 如果oldVnode的第一个child不存在
      if (isUndef(oldStartVnode)) {
        // oldStart索引右移
        oldStartVnode = oldCh[++oldStartIdx] // Vnode has been moved left

      // 如果oldVnode的最后一个child不存在
      } else if (isUndef(oldEndVnode)) {
        // oldEnd索引左移
        oldEndVnode = oldCh[--oldEndIdx]

      // oldStartVnode和newStartVnode是同一个节点
      } else if (sameVnode(oldStartVnode, newStartVnode)) {
        // patch oldStartVnode和newStartVnode， 索引左移，继续循环
        patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue)
        oldStartVnode = oldCh[++oldStartIdx]
        newStartVnode = newCh[++newStartIdx]

      // oldEndVnode和newEndVnode是同一个节点
      } else if (sameVnode(oldEndVnode, newEndVnode)) {
        // patch oldEndVnode和newEndVnode，索引右移，继续循环
        patchVnode(oldEndVnode, newEndVnode, insertedVnodeQueue)
        oldEndVnode = oldCh[--oldEndIdx]
        newEndVnode = newCh[--newEndIdx]

      // oldStartVnode和newEndVnode是同一个节点
      } else if (sameVnode(oldStartVnode, newEndVnode)) { // Vnode moved right
        // patch oldStartVnode和newEndVnode
        patchVnode(oldStartVnode, newEndVnode, insertedVnodeQueue)
        // 如果removeOnly是false，则将oldStartVnode.eml移动到oldEndVnode.elm之后
        canMove && nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm))
        // oldStart索引右移，newEnd索引左移
        oldStartVnode = oldCh[++oldStartIdx]
        newEndVnode = newCh[--newEndIdx]

      // 如果oldEndVnode和newStartVnode是同一个节点
      } else if (sameVnode(oldEndVnode, newStartVnode)) { // Vnode moved left
        // patch oldEndVnode和newStartVnode
        patchVnode(oldEndVnode, newStartVnode, insertedVnodeQueue)
        // 如果removeOnly是false，则将oldEndVnode.elm移动到oldStartVnode.elm之前
        canMove && nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm)
        // oldEnd索引左移，newStart索引右移
        oldEndVnode = oldCh[--oldEndIdx]
        newStartVnode = newCh[++newStartIdx]

      // 如果都不匹配
      } else {
        if (isUndef(oldKeyToIdx)) oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx)

        // 尝试在oldChildren中寻找和newStartVnode的具有相同的key的Vnode
        idxInOld = isDef(newStartVnode.key)
          ? oldKeyToIdx[newStartVnode.key]
          : findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx)

        // 如果未找到，说明newStartVnode是一个新的节点
        if (isUndef(idxInOld)) { // New element
          // 创建一个新Vnode
          createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm)

        // 如果找到了和newStartVnodej具有相同的key的Vnode，叫vnodeToMove
        } else {
          vnodeToMove = oldCh[idxInOld]
          /* istanbul ignore if */
          if (process.env.NODE_ENV !== 'production' && !vnodeToMove) {
            warn(
              'It seems there are duplicate keys that is causing an update error. ' +
              'Make sure each v-for item has a unique key.'
            )
          }

          // 比较两个具有相同的key的新节点是否是同一个节点
          //不设key，newCh和oldCh只会进行头尾两端的相互比较，设key后，除了头尾两端的比较外，还会从用key生成的对象oldKeyToIdx中查找匹配的节点，所以为节点设置key可以更高效的利用dom。
          if (sameVnode(vnodeToMove, newStartVnode)) {
            // patch vnodeToMove和newStartVnode
            patchVnode(vnodeToMove, newStartVnode, insertedVnodeQueue)
            // 清除
            oldCh[idxInOld] = undefined
            // 如果removeOnly是false，则将找到的和newStartVnodej具有相同的key的Vnode，叫vnodeToMove.elm
            // 移动到oldStartVnode.elm之前
            canMove && nodeOps.insertBefore(parentElm, vnodeToMove.elm, oldStartVnode.elm)

          // 如果key相同，但是节点不相同，则创建一个新的节点
          } else {
            // same key but different element. treat as new element
            createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm)
          }
        }

        // 右移
        newStartVnode = newCh[++newStartIdx]
      }
    }
```
while循环主要处理了以下五种情景：

* 当新老 VNode 节点的 start 相同时，直接 patchVnode ，同时新老 VNode 节点的开始索引都加 1
* 当新老 VNode 节点的 end相同时，同样直接 patchVnode ，同时新老 VNode 节点的结束索引都减 1
* 当老 VNode 节点的 start 和新 VNode 节点的 end 相同时，这时候在 patchVnode 后，还需要将当前真实 dom 节点移动到 oldEndVnode 的后面，同时老 VNode 节点开始索引加 1，新 VNode 节点的结束索引减 1
* 当老 VNode 节点的 end 和新 VNode 节点的 start 相同时，这时候在 patchVnode 后，还需要将当前真实 dom 节点移动到 oldStartVnode 的前面，同时老 VNode 节点结束索引减 1，新 VNode 节点的开始索引加 1
* 如果都不满足以上四种情形，那说明没有相同的节点可以复用，则会分为以下两种情况：
  * 从旧的 VNode 为 key 值，对应 index 序列为 value 值的哈希表中找到与 newStartVnode 一致 key 的旧的 VNode 节点，再进行patchVnode ，同时将这个真实 dom 移动到 oldStartVnode 对应的真实 dom 的前面
  * 调用 createElm 创建一个新的 dom 节点放到当前 newStartIdx 的位置
### 小结
* 当数据发生改变时，订阅者watcher就会调用patch给真实的DOM打补丁
* 通过isSameVnode进行判断，相同则调用patchVnode方法
* patchVnode做了以下操作：
  * 找到对应的真实dom，称为el
  * 如果都有都有文本节点且不相等，将el文本节点设置为Vnode的文本节点
  * 如果oldVnode有子节点而VNode没有，则删除el子节点
  * 如果oldVnode没有子节点而VNode有，则将VNode的子节点真实化后添加到el
  * 如果两者都有子节点，则执行updateChildren函数比较子节点
* updateChildren主要做了以下操作：
  * 设置新旧VNode的头尾指针
  * 新旧头尾指针进行比较，循环向中间靠拢，根据情况调用patchVnode进行patch重复流程、调用createElem创建一个新节点，从哈希表寻找 key一致的VNode 节点再分情况操作
### 参考
[你了解diff算法吗](https://www.developers.pub/wiki/1065322/1065448)

[虚拟DOM & diff算法](https://juejin.cn/post/6844903887162310669#comment)

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
