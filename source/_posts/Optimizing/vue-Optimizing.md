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
* v-for 增加 key 使diff算法更快速
* 不需要响应式处理的数据可以通过`Object.freeze`处理，或者直接通过`this.xxx = xxx`的方式进行定义
* 避免内存泄漏，合理定义变量，计时器，监听事件销毁回收
* 长列表性能优化，例如虚拟渲染，滚动加载等等
* 图片懒加载 https://blog.csdn.net/mynewdays/article/details/124446128
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
* 减小入口文件体积
* 开启 gzip 压缩
* 浏览器缓存
* CDN 的使用
* 使用 Chrome Performance 查找性能瓶颈

## 其他
* 尽量使用图标，不用图片

## 首屏性能优化

### 分析加载慢的原因
利用 `performance.timing` 提供的数据计算首屏时间。
在页面渲染的过程，导致加载速度慢的因素可能如下：
* 网络延时问题
* 资源文件体积是否过大
* 资源是否重复发送请求去加载了
* 加载脚本的时候，渲染内容堵塞了
### 解决方案
常见的几种SPA首屏优化方式： 
* 减小入口文件体积
* 静态资源本地缓存
* UI框架按需加载
* 图片资源的压缩
* 避免组件重复打包
* 开启GZip压缩
* 使用SSR

#### 减小入口文件体积
路由懒加载，把不同路由对应的组件分割成不同的代码块，待路由被请求的时候会单独打包路由，使得入口文件变小，加载速度大大增加.  
在 `vue-router` 配置路由的时候，采用动态加载路由的形式
```js
  routes:[ 
    path: 'Blogs',
    name: 'ShowBlogs',
    component: () => import('./components/ShowBlogs.vue')
  ]
```
以函数的形式加载路由，这样就可以把各自的路由文件分别打包，只有在解析给定的路由时，才会加载路由组件

#### 静态资源本地缓存
后端返回资源问题：  
* 采用HTTP缓存，设置Cache-Control，Last-Modified，Etag等响应头
* 采用Service Worker离线缓存   
前端合理利用 `localStorage`

#### UI框架按需加载
```js
// 直接引用整个UI库
import ElementUI from 'element-ui'
Vue.use(ElementUI)

// 按需引用
import { Button, Input, Pagination, Table, TableColumn, MessageBox } from 'element-ui';
Vue.use(Button)
Vue.use(Input)
Vue.use(Pagination)
```
#### 避免组件重复打包
假设`A.js`文件是一个常用的库，现在有多个路由使用了`A.js`文件，这就造成了重复下载，解决方案：在`webpack`的`config`文件中，修改`CommonsChunkPlugin`的配置:
```js
minChunks: 3
```
`minChunks为3` 表示会把使用3次及以上的包抽离出来，放进公共依赖文件，避免了重复加载组件

#### 图片资源的压缩
图片资源虽然不在编码过程中，但它却是对页面性能影响最大的因素，对于所有的图片资源，我们可以进行适当的压缩，对页面上使用到的`icon`，可以使用在线字体图标，或者雪碧图，将众多小图标合并到同一张图上，用以减轻http请求压力。

#### 开启GZip压缩
拆完包之后，我们再用gzip做一下压缩 安装`compression-webpack-plugin`
```js
cnmp i compression-webpack-plugin -D
```
在`vue.congig.js`中引入并修改webpack配置
```js
const CompressionPlugin = require('compression-webpack-plugin')
configureWebpack: (config) => {
    if (process.env.NODE_ENV === 'production') {
        // 为生产环境修改配置...
        config.mode = 'production'
        return {
            plugins: [new CompressionPlugin({
                test: /\.js$|\.html$|\.css/, //匹配文件名
                threshold: 10240, //对超过10k的数据进行压缩
                deleteOriginalAssets: false //是否删除原文件
            })]
        }
    }
```
在服务器我们也要做相应的配置 如果发送请求的浏览器支持`gzip`，就发送给它`gzip`格式的文件 我的服务器是用express框架搭建的 只要安装一下`compression`就能使用
```js
const compression = require('compression')
app.use(compression())  // 在其他中间件使用之前调用
```
#### 使用SSR
`SSR（Server side ）`，也就是服务端渲染，组件或页面通过服务器生成html字符串，再发送到浏览器，从头搭建一个服务端渲染是很复杂的，vue应用建议使用`Nuxt.js`实现服务端渲染


### 参考
* [SPA（单页应用）首屏加载速度慢怎么解决？](https://fe.ecool.fun/topic/a5032eb9-6a53-4792-852f-3f9417631c47?orderBy=updateTime&order=desc&tagId=14)
* [首屏加载速度优化](https://www.developers.pub/wiki/1065322/1065368)

## 参考
[Vue性能优化](https://juejin.cn/post/6844903918753808398#heading-18)

[当面试官问我前端可以做的性能优化有哪些](https://juejin.cn/post/7194400984490049573)