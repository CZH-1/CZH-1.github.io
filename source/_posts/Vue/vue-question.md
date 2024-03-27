---
title: "vue-question"
date: 2024-02-21 20:53:22
categories:
- Vue
tags:
- Vue
toc: true # 是否显示目录
---

> * 对 nextTick 的理解
> * 对 mixin 的理解
> * 对 slot 的理解
> * 对 Observable 的理解
> * vue中key的原理
> * vue权限管理
> * render
> * new Vue
> * bus
> * vue2 与 vue3 的区别

<!-- more -->
## nextTick 
> 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM

Vue 在更新 DOM 时是异步执行的。当数据发生变化，Vue将开启一个异步更新队列，视图需要等队列中所有数据变化完成之后，再统一进行更新

$nextTick() 会返回一个 Promise 对象，可以是用async/await完成相同作用的事情

```js
this.message = '修改后的值'
console.log(this.$el.textContent) // => '原始的值'
await this.$nextTick()
console.log(this.$el.textContent) // => '修改后的值'
```
### 参考 
[说说你对nextTick的理解？](https://www.developers.pub/wiki/1065322/1065413)

## mixin
> mixin（混入），提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。

本质其实就是一个js对象，它可以包含我们组件中任意功能选项，如data、components、methods 、created、computed等等。我们只要将共用的功能以对象的方式传入 mixins选项中，当组件使用 `mixins` 对象时所有 `mixins` 对象的选项都将被混入该组件本身的选项中来。

> 注意事项：当组件存在与mixin对象相同的选项的时候，进行递归合并的时候组件的选项会覆盖mixin的选项，但是如果相同选项为生命周期钩子的时候，会合并成一个数组，先执行mixin的钩子，再执行组件的钩子

## slot

* 默认插槽
* 具名插槽
* 作用域插槽
  
### 默认插槽
```js
// 子组件
<template>
  <slot>
    <p>插槽后备的内容</p>
  </slot>
</template>

// 父组件
<Child>
  <div>默认插槽</div>  
</Child>
```
### 具名插槽
```js
// 子组件
<template>
  <slot>插槽后备的内容</slot>
  <slot name="content">插槽后备的内容</slot>
</template>

// 父组件
<child>
  <template v-slot:default>具名插槽</template>
  <!-- 具名插槽⽤插槽名做参数 -->
  <template v-slot:content>内容...</template>
</child>
```
### 作用域插槽
```javascript
// 子组件
<template> 
  <slot name="footer" testProps="子组件的值">
    <h3>没传footer插槽</h3>
  </slot>
</template>

// 父组件
<child> 
  <!-- 把v-slot的值指定为作⽤域上下⽂对象 -->
  <template v-slot:default="slotProps">
    来⾃⼦组件数据：{{slotProps.testProps}}
  </template>
  <template #default="slotProps">
    来⾃⼦组件数据：{{slotProps.testProps}}
  </template>
</child>
```
### 小结
* v-slot属性只能在 `template` 上使用，但在只有默认插槽时可以在组件标签上使用
* 默认插槽名为default，可以省略default直接写v-slot 
* 缩写为#时不能不写参数，写成#default
* 可以通过解构获取`v-slot={user}`，还可以重命名`v-slot="{user: newName}"`和定义默认值`v-slot="{user = '默认值'}"`

## Observable
> `Vue.observable` 让一个对象变成响应式数据。Vue 内部会用它来处理 data 函数返回的对象

返回的对象可以直接用于渲染函数和计算属性内，并且会在发生变更时触发相应的更新。也可以作为最小化的跨组件状态存储器

```javascript
Vue.observable({ count : 1})
// 其作用等同于
new vue({ count : 1})
```
实现：
```js
// store.js
import Vue from "vue"
export const state = Vue.observable({
  count: 0
})
export const mutations = {
  add(payload) {
      if (payload > 0) {
          state.count = payload
      }
  },
  reduce(payload) {
      if (payload > 0) {
          state.count = payload
      }
  }
}

// page.vue
<template>
  <div class="observable">
    <h4>Observable1</h4>
    <div class="observable-layout">
      <button @click="add">add</button>
      <h4>{{ count }}</h4>
      <button @click="reduce">reduce</button>
    </div>
    <div><button @click="go">go to Observable2</button></div>
  </div>
</template>
<script>
import { state } from "./store.js";
export default {
  data() {
    return {};
  },
  computed: {
    count() {
      return state.count;
    },
  },
  methods: {
    add() {
      state.count++;
    },
    reduce() {
      state.count--;
    },
    go() {
      this.$router.push({ name: "About" });
    },
  },
};
</script>
```
在 Vue 2.x 中，被传入的对象会直接被 `Vue.observable` 变更，它和被返回的对象是同一个对象   
在 Vue 3.x 中，则会返回一个可响应的代理，而对源对象直接进行变更仍然是不可响应的
### 使用场景
在非父子组件通信时，可以使用通常的bus或者使用vuex，但是实现的功能不是太复杂，而使用上面两个又有点繁琐。这时，observable就是一个很好的选择。
```js
// 创建js文件
// 引入vue
import Vue from 'vue
// 创建state对象，使用observable让state对象可响应
export let state = Vue.observable({
  name: '张三',
  'age': 38
})
// 创建对应的方法
export let mutations = {
  changeName(name) {
    state.name = name
  },
  setAge(age) {
    state.age = age
  }
}
```
```js
// .vue文件中使用
<template>
  <div>
    姓名：{{ name }}
    年龄：{{ age }}
    <button @click="changeName('李四')">改变姓名</button>
    <button @click="setAge(18)">改变年龄</button>
  </div>
</template>
import { state, mutations } from '@/store
export default {
  // 在计算属性中拿到值
  computed: {
    name() {
      return state.name
    },
    age() {
      return state.age
    }
  },
  // 调用mutations里面的方法，更新数据
  methods: {
    changeName: mutations.changeName,
    setAge: mutations.setAge
  }
}
```

## vue中key的原理
> key是给每一个 `vnode` 的唯一id，也是 `diff` 的一种优化策略，可以根据key，更准确， 更快的找到对应的vnode节点
### 背后的逻辑
当我们在使用v-for时，需要给单元加上key：
* 如果不用key，Vue会采用就地复地原则：最小化element的移动，并且会尝试尽最大程度在同适当的地方对相同类型的element，做patch或者reuse。
* 如果使用了key，Vue会根据keys的顺序记录element，曾经拥有了key的element如果不再出现的话，会被直接remove或者destoryed
### 区别
```javascript
<body>
  <div id="demo">
    <p v-for="item in items" :key="item">{{item}}</p>
  </div>
  <script src="../../dist/vue.js"></script>
  <script>
    // 创建实例
    const app = new Vue({
      el: '#demo',
      data: { items: ['a', 'b', 'c', 'd', 'e'] },
      mounted () {
        setTimeout(() => { 
          this.items.splice(2, 0, 'f')  // 
       }, 2000);
     },
   });
  </script>
</body>
```
在不使用key的情况，vue会进行这样的操作：  
* 比较A，A，相同类型的节点，进行patch，但数据相同，不发生dom操作
* 比较B，B，相同类型的节点，进行patch，但数据相同，不发生dom操作
* 比较C，F，相同类型的节点，进行patch，数据不同，发生dom操作
* 比较D，C，相同类型的节点，进行patch，数据不同，发生dom操作
* 比较E，D，相同类型的节点，进行patch，数据不同，发生dom操作
循环结束，将E插入到DOM中

一共发生了3次更新，1次插入操作

在使用key的情况：vue会进行这样的操作：

* 比较A，A，相同类型的节点，进行patch，但数据相同，不发生dom操作
* 比较B，B，相同类型的节点，进行patch，但数据相同，不发生dom操作
* 比较C，F，不相同类型的节点
* 比较E、E，相同类型的节点，进行patch，但数据相同，不发生dom操作
* 比较D、D，相同类型的节点，进行patch，但数据相同，不发生dom操作
* 比较C、C，相同类型的节点，进行patch，但数据相同，不发生dom操作
循环结束，将F插入到C之前

一共发生了0次更新，1次插入操作

通过上面两个小例子，可见设置key能够大大减少对页面的DOM操作，提高了diff效率
### 设置key值一定能提高diff效率吗？
其实不然，官方文档明确表示：
> 当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用“就地复用”策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素

这个默认的模式是高效的，但是只适用于不依赖子组件状态或临时 DOM 状态 (例如：表单输入值) 的列表渲染输出。

建议尽可能在使用 v-for 时提供 key，除非遍历输出的 DOM 内容非常简单，或者是刻意依赖默认行为以获取性能上的提升。

## vue权限管理

* 接口权限(返回401)
* 路由权限
* 菜单权限
* 按钮权限

## render
render: 
  对template的解析步骤大致分为以下几步：
    将html文档片段解析成ast描述符
    将ast描述符解析成字符串
    生成render函数
    生成render函数，挂载到vm上后，会再次调用mount方法
    render的作用主要是生成vnode
    _update主要功能是调用patch，将vnode转换为真实DOM，并且更新到页面中

## new Vue
new Vue 挂载发生的过程：
  new Vue的时候调用会调用_init方法
  定义 $set、 $get 、$delete、$watch 等方法
  定义 $on、$off、$emit、$off 等事件
  定义 _update、$forceUpdate、$destroy生命周期
  调用$mount进行页面的挂载
  挂载的时候主要是通过mountComponent方法
  定义updateComponent更新函数
  执行render生成虚拟DOM
  _update将虚拟DOM生成真实DOM结构，并且渲染到页面中

## Bus
```js
import Vue from 'vue'
const Bus = new Vue()
Vue.prototype.bus = new Vue();
new Vue({
 render: h => h(App)
  ...
}).$mount('#app');
/*
*通过把一个vue实例赋于Vue构造函数原型上的一个属性bus（当然起任何名称都是可以的）
*而每个Vue实例都是有$emit和$on方法的
*由于bus属性在Vue原型上，根据原型链查找规则，在页面中我们就可以通过 this.bus.$emit 和 
*this.bus.$on来进行跨组件通信了
*/
```

## vue2 与 vue3的区别
* 打包体积更小
* 性能优化：Vue.js 3.0使用了 `Proxy` 替代 `Object.defineProperty` 实现响应式，并且使用了静态提升技术来提高渲染性能。新增了编译时优化，在编译时进行模板静态分析，并生成更高效的渲染函数。
* Composition API：Composition API是一个全新的组件逻辑复用方式，可以更好地组合和复用组件的逻辑。
* TypeScript支持：Vue.js 3.0完全支持TypeScript，在编写Vue应用程序时可以更方便地利用TS的类型检查和自动补全功能。
* 新的自定义渲染API：Vue.js 3.0的自定义渲染API允许开发者在细粒度上控制组件渲染行为，包括自定义渲染器、组件事件和生命周期等。
* 改进的Vue CLI：Vue.js 3.0使用了改进的Vue CLI，可以更加灵活地配置项目，同时支持Vue.js2.x项目升级到Vue.js 3.0。
* 移除一些API：Vue.js 3.0移除了一些不常用的API，如过渡相关API，部分修饰符，.sync等。
* diff 算法

### Proxy和Object.defineProperty的区别？
`Proxy` 和 `Object.defineProperty` 都可以用来实现JavaScript对象的响应式，但是它们有一些区别：
* 实现方式：`Proxy` 是ES6新增的一种特性，使用了一种代理机制来实现响应式。而 `Object.defineProperty` 是在ES5中引入的，使用了getter和setter方法来实现。
* 作用对象：Proxy可以代理整个对象，包括对象的所有属性、数组的所有元素以及类似数组对象的所有元素。而Object.defineProperty只能代理对象上定义的属性。
* 监听属性：Proxy可以监听到新增属性和删除属性的操作，而Object.defineProperty只能监听到已经定义的属性的变化。
* 性能：由于Proxy是ES6新增特性，其内部实现采用了更加高效的算法，相对于Object.defineProperty来说在性能方面有一定的优势。

综上所述，虽然Object.defineProperty在Vue.js 2.x中用来实现响应式，但是在Vue.js 3.0中已经采用了Proxy来替代，这是因为Proxy相对于Object.defineProperty拥有更优异的性能和更强大的能力。


## 参考 
* [Vue系列题集](https://www.developers.pub/wiki/1065322/1065413)
* [Vue3与Vue2的优势及区别](https://juejin.cn/post/7227453567686033468)