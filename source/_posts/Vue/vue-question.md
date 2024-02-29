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

<!-- more -->
## NextTick 
> 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM

Vue 在更新 DOM 时是异步执行的。当数据发生变化，Vue将开启一个异步更新队列，视图需要等队列中所有数据变化完成之后，再统一进行更新

### 参考 [说说你对nextTick的理解？](https://www.developers.pub/wiki/1065322/1065413)

## mixin
> mixin（混入），提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。

本质其实就是一个js对象，它可以包含我们组件中任意功能选项，如data、components、methods 、created、computed等等。我们只要将共用的功能以对象的方式传入 mixins选项中，当组件使用 mixins对象时所有mixins对象的选项都将被混入该组件本身的选项中来。

> 注意事项：当组件存在与mixin对象相同的选项的时候，进行递归合并的时候组件的选项会覆盖mixin的选项，但是如果相同选项为生命周期钩子的时候，会合并成一个数组，先执行mixin的钩子，再执行组件的钩子

## slot

* 默认插槽
* 具名插槽
* 作用域插槽
  
### 默认插槽
```bash
# 子组件
<template>
  <slot>
    <p>插槽后备的内容</p>
  </slot>
</template>

#父组件
<Child>
  <div>默认插槽</div>  
</Child>
```
### 具名插槽
```bash
# 子组件
<template>
  <slot>插槽后备的内容</slot>
  <slot name="content">插槽后备的内容</slot>
</template>

#父组件
<child>
  <template v-slot:default>具名插槽</template>
  <!-- 具名插槽⽤插槽名做参数 -->
  <template v-slot:content>内容...</template>
</child>
```
### 作用域插槽
```bash
# 子组件
<template> 
  <slot name="footer" testProps="子组件的值">
    <h3>没传footer插槽</h3>
  </slot>
</template>

#父组件
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
> * v-slot属性只能在 template 上使用，但在只有默认插槽时可以在组件标签上使用
> * 默认插槽名为default，可以省略default直接写v-slot
> * 缩写为#时不能不写参数，写成#default
> * 可以通过解构获取v-slot={user}，还可以重命名v-slot="{user: newName}"和定义默认值v-slot="{user = '默认值'}"

## Observable
> Vue.observable，让一个对象变成响应式数据。Vue 内部会用它来处理 data 函数返回的对象

返回的对象可以直接用于渲染函数和计算属性内，并且会在发生变更时触发相应的更新。也可以作为最小化的跨组件状态存储器

```bash
Vue.observable({ count : 1})
其作用等同于
new vue({ count : 1})
```
在 Vue 2.x 中，被传入的对象会直接被 Vue.observable 变更，它和被返回的对象是同一个对象

在 Vue 3.x 中，则会返回一个可响应的代理，而对源对象直接进行变更仍然是不可响应的
### 使用场景
在非父子组件通信时，可以使用通常的bus或者使用vuex，但是实现的功能不是太复杂，而使用上面两个又有点繁琐。这时，observable就是一个很好的选择。

## vue中key的原理
> key是给每一个vnode的唯一id，也是diff的一种优化策略，可以根据key，更准确， 更快的找到对应的vnode节点
### 背后的逻辑
当我们在使用v-for时，需要给单元加上key：
 * 如果不用key，Vue会采用就地复地原则：最小化element的移动，并且会尝试尽最大程度在同适当的地方对相同类型的element，做patch或者reuse。
 * 如果使用了key，Vue会根据keys的顺序记录element，曾经拥有了key的element如果不再出现的话，会被直接remove或者destoryed
### 区别
```bash
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

分析下整体流程：

比较A，A，相同类型的节点，进行patch，但数据相同，不发生dom操作
比较B，B，相同类型的节点，进行patch，但数据相同，不发生dom操作
比较C，F，相同类型的节点，进行patch，数据不同，发生dom操作
比较D，C，相同类型的节点，进行patch，数据不同，发生dom操作
比较E，D，相同类型的节点，进行patch，数据不同，发生dom操作
循环结束，将E插入到DOM中

一共发生了3次更新，1次插入操作

在使用key的情况：vue会进行这样的操作：

比较A，A，相同类型的节点，进行patch，但数据相同，不发生dom操作
比较B，B，相同类型的节点，进行patch，但数据相同，不发生dom操作
比较C，F，不相同类型的节点
比较E、E，相同类型的节点，进行patch，但数据相同，不发生dom操作
比较D、D，相同类型的节点，进行patch，但数据相同，不发生dom操作
比较C、C，相同类型的节点，进行patch，但数据相同，不发生dom操作
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


## 参考 [Vue系列题集](https://www.developers.pub/wiki/1065322/1065413)