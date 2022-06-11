---
title: "理解JS Event Loop"
date: 2022-03-12 12:22:54
categories:
- JavaScript
tags:
- JavaScript
toc: true # 是否显示目录
---

> Event Loop
> 同步/异步

<!-- more -->
# 基本概念
  ## 执行 & 运行
  JavaScript 的执行和运行是两个不同概念的，执行，一般依赖于环境，比如 node、浏览器 等， JavaScript 在不同环境下的执行机制可能并不相同。而今天我们要讨论的 Event Loop 就是 JavaScript 的一种执行方式。而运行呢，是指JavaScript 的解析引擎，这是统一的。
  ## JavaScript Engine 和 JavaScript Runtime
  为了让 JavaScript 运行起来，要完成两部分工作（当然实际比这复杂的多）：
   * 编译并执行 JavaScript 代码，完成内存分配、垃圾回收等；
   * 为 JavaScript 提供一些对象或机制，使它能够与外界交互。
  
  这里的第一部分，是 Engine（执行引擎）；第二部分，是 Runtime（执行环境）。


# 关于 JavaScript
   * JavaScript 是单线程语言，只有一个调用栈，决定了它在同一时间只能做一件事情。 在最新的HTML5中提出了Web-Worker，但javascript是单线程这一核心仍未改变。所以一切javascript版的"多线程"都是用单线程模拟出来的，一切javascript多线程都是纸老虎！
   * 在 JavaScript 运行的时候，JavaScript Engine 会创建和维护相应的堆（Heap）和栈（Stack），同时通过 JavaScript Runtime 提供的一系列 API（例如 setTimeout、XMLHttpRequest 等）来完成各种各样的任务。
   * 在 JavaScript 的运行过程中，真正负责执行 JavaScript 代码的始终只有一个线程，通常被称为主线程，各种任务都会用排队的方式来同步执行。这种方式最常见的一个问题就是：如果你尝试执行一段非常耗时的同步代码，浏览器就没办法同时去渲染 GUI，导致界面失去响应，也就是被阻塞了。

# 事件循环（Event Loop）
## 什么是 Event Loop？
  事件循环（Event Loop） 是让 JavaScript 做到既是单线程，又绝对不会阻塞的核心机制，也是 JavaScript 并发模型（Concurrency Model）的基础，是用来协调各种事件、用户交互、脚本执行、UI 渲染、网络请求等的一种机制。
  简单一点：Event Loop 只不过是实现异步的一种机制而已。
  JavaScript 有一个主线程 main thread，和调用栈 call-stack 也称之为执行栈。所有的任务都会放到调用栈中等待主线程来执行。
## 任务队列(task queue)
执行和协调各种任务时，Event Loop 会维护自己的任务队列。任务队列又分为 Task Queue 和 Microtask Queue 两种。
> 实际上，称任务队列为事件队列（Event Queue）可能会更容易理解。所谓的事件驱动（Event-driven），就是将一切抽象为事件（Event），比如 AJAX 完成、鼠标点击、I/O 操作等等，都是一个个的事件，而 Event Loop 就是一个事件循环的过程。
同步任务(SyncTask): 主线程来执行的时候立即就能执行的代码
异步任务(AsyncTask): 先去执行别的 task，等我这 xxx 完之后再往 Task Queue 里面塞一个 task 的同步任务来等待被执行

![](http://blog.czhhx.cn/e1.png)
- 主线程自上而下执行所有代码
- 同步任务直接进入到主线程被执行，而异步任务则进入到 Event Table 并注册相对应的回调函数
- 异步任务完成后，Event Table 会将这个函数移入 Event Queue
- 主线程任务执行完了以后，会从Event Queue中读取任务，进入到主线程去执行。
- 循环如上

我们不禁要问了，那怎么知道主线程执行栈为空啊？js引擎存在monitoring process进程，会持续不断的检查主线程执行栈是否为空，一旦为空，就会去Event Queue那里检查是否有等待被调用的函数。

上述动作不断循环，就是我们所说的事件循环(Event Loop)。

例如：
```bash
ajax({
    url:www.Nealyang.com,
    data:prams,
    success:() => {
        console.log('请求成功!');
    },
    error:()=>{
        console.log('请求失败~');
    }
})
console.log('这是一个同步任务');

```
- ajax 请求首先进入到 Event Table ，分别注册了onError和onSuccess回调函数。
- 主线程执行同步任务：console.log('这是一个同步任务');
- 主线程任务执行完毕，看Event Queue是否有待执行的 task,这里是不断地检查，只要主线程的task queue没有任务执行了，主线程就一直在这等着
- ajax 执行完毕，将回调函数push 到Event Queue。（步骤 3、4 没有先后顺序而言）
- 主线程“终于”等到了Event Queue里有 task可以执行了，执行对应的回调任务。
- 如此往复。


### Task Queue(宏任务队列)
一个 Event Loop 会有一个或多个 Task Queue，这是一个先进先出（FIFO）的有序列表，存放着来自不同 Task Source（任务源）的 Task。
在 HTML 标准中，定义了几种常见的 Task Source:
* DOM manipulation（DOM 操作）；
* User interaction（用户交互）；
* Networking（网络请求）；
* History traversal（History API 操作）。

Task Source 的定义非常的宽泛,包括整体代码script，setTimeout，setInterval，常见的鼠标、键盘事件，AJAX，数据库操作（例如 IndexedDB），等等都属于 Task Source，所有来自这些 Task Source 的 Task 都会被放到对应的 Task Queue 中等待处理。

### Microtask Queue(微任务队列)
Microtask Queue 与 Task Queue 类似，也是一个有序列表。不同之处在于，一个 Event Loop 只有一个 Microtask Queue。
在 HTML 标准中，并没有明确规定 Microtask Source，通常认为有以下几种：
* Promise.then catch finally(注意我不是说 Promise)
* Process.nextTick
* MutationObserver

### setTimeout
setTimeout无需再多言，大家对他的第一印象就是异步可以延时执行，渐渐的setTimeout用的地方多了，问题也出现了，有时候明明写的延时3秒，实际却5，6秒才执行函数，这又咋回事啊？
看一个例子：
```bash
setTimeout(() => {
    task();
},3000)
console.log('执行console');

```
前面我们的结论，setTimeout是异步的，应该先执行console.log这个同步任务，所以我们的结论是：
```bash
//执行console
//task()
```
去验证一下，结果正确！ 然后我们修改一下前面的代码：
```bash
setTimeout(() => {
    task()
},3000)

sleep(10000000)

```
乍一看其实差不多嘛，但我们把这段代码在chrome执行一下，却发现控制台执行task()需要的时间远远超过3秒，说好的延时三秒，为啥现在需要这么长时间啊？
这时候我们需要重新理解setTimeout的定义。我们先说上述代码是怎么执行的：
- task()进入Event Table并注册,计时开始。
- 执行sleep函数，很慢，非常慢，计时仍在继续。
- 3秒到了，计时事件timeout完成，task()进入Event Queue，但是sleep也太慢了吧，还没执行完，只好等着。
- sleep终于执行完了，task()终于从Event Queue进入了主线程执行。

上述的流程走完，我们知道setTimeout这个函数，是经过指定时间后，把要执行的任务(本例中为task())加入到Event Queue中，又因为是单线程任务要一个一个执行，如果前面的任务需要的时间太久，那么只能等着，导致真正的延迟时间远远大于3秒。
我们还经常遇到setTimeout(fn,0)这样的代码，0秒后执行又是什么意思呢？是不是可以立即执行呢？
答案是不会的，setTimeout(fn,0)的含义是，指定某个任务在主线程最早可得的空闲时间执行，意思就是不用再等多少秒了，只要主线程执行栈内的同步任务全部执行完成，栈为空就马上执行。举例说明：
```bash
//代码1
console.log('先执行这里');
setTimeout(() => {
    console.log('执行啦')
},0);

```
```bash
//代码2
console.log('先执行这里');
setTimeout(() => {
    console.log('执行啦')
},3000);  


```
代码1的输出结果是：

```bash
//先执行这里
//执行啦


```
代码2的输出结果是：

```bash
//先执行这里
// ... 3s later
// 执行啦

```
关于setTimeout要补充的是，即便主线程为空，0毫秒实际上也是达不到的。根据HTML的标准，最低是4毫秒。有兴趣的同学可以自行了解。

### setInterval
上面说完了setTimeout，当然不能错过它的孪生兄弟setInterval。他俩差不多，只不过后者是循环的执行。对于执行顺序来说，setInterval会每隔指定的时间将注册的函数置入Event Queue，如果前面的任务耗时太久，那么同样需要等待。
唯一需要注意的一点是，对于setInterval(fn,ms)来说，我们已经知道不是每过ms秒会执行一次fn，而是每过ms秒，会有fn进入Event Queue。一旦setInterval的回调函数fn执行时间超过了延迟时间ms，那么就完全看不出来有时间间隔了。

## 事件循环执行顺序
整体script作为第一个宏任务进入主线程执行栈进，将宏任务(例如setTimeout)，其回调函数存放入宏任务队列中，微任务(例如process.nextTick())，将其回调函数存放入微任务队列中（队列：先进先出FIFO），所有异步任务都存在调用栈（栈：后进先出LIFO）中。开始第一次循环，接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。
如下：
```bash 
setTimeout(function() {
    console.log('setTimeout');
})

new Promise(function(resolve) {
    console.log('promise');
    resolve() // 注意：若无resolve将报错
}).then(function() {
    console.log('then');
})

console.log('console');
```
- 这段代码作为宏任务，进入主线程。
- 先遇到setTimeout，那么将其回调函数注册后分发到宏任务Event Queue。(注册过程与上同，下文不再描述)
- 接下来遇到了Promise，new Promise立即执行，then函数分发到微任务Event Queue。
- 遇到console.log()，立即执行。
- 好啦，整体代码script作为第一个宏任务执行结束，看看有哪些微任务？我们发现了then在微任务Event Queue里面，执行。
- ok，第一轮事件循环结束了，我们开始第二轮循环，当然要从宏任务Event Queue开始。我们发现了宏任务Event Queue中setTimeout对应的回调函数，立即执行。
- 结束。
结果如下：// promise console then setTimeout

事件循环，宏任务，微任务的关系如图所示：

![](http://blog.czhhx.cn/e2.png)

分析一段较复杂的代码，看看你是否真的掌握了js的执行机制：
```bash
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})

```
事件循环流程分析如下：
- 整体script作为第一个宏任务进入主线程，遇到console.log，输出1。
- 遇到setTimeout，其回调函数被分发到宏任务Event Queue中。我们暂且记为setTimeout1。
- 遇到process.nextTick()，其回调函数被分发到微任务Event Queue中。我们记为process1。
- 遇到Promise，new Promise直接执行，输出7。then被分发到微任务Event Queue中。我们记为then1。
- 又遇到了setTimeout，其回调函数被分发到宏任务Event Queue中，我们记为setTimeout2。
- 上表是第一轮事件循环宏任务结束时各Event Queue的情况，此时已经输出了1和7。

- 我们发现了process1和then1两个微任务。

- 执行process1,输出6。

- 执行then1，输出8。

好了，第一轮事件循环正式结束，这一轮的结果是输出1，7，6，8。那么第二轮时间循环从setTimeout1宏任务开始：
- 首先输出2。接下来遇到了process.nextTick()，同样将其分发到微任务Event Queue中，记为process2。new Promise立即执行输出4，then也分发到微任务Event Queue中，记为then2。
- 第二轮事件循环宏任务结束，我们发现有process2和then2两个微任务可以执行。
- 输出3。
- 输出5。
- 第二轮事件循环结束，第二轮输出2，4，3，5。
- 第三轮事件循环开始，此时只剩setTimeout2了，执行。
- 直接输出9。
- 将process.nextTick()分发到微任务Event Queue中。记为process3。
- 直接执行new Promise，输出11。
- 将then分发到微任务Event Queue中，记为then3。
- 第三轮事件循环宏任务执行结束，执行两个微任务process3和then3。
- 输出10。
- 输出12。
- 第三轮事件循环结束，第三轮输出9，11，10，12。

整段代码，共进行了三次事件循环，完整的输出为1，7，6，8，2，4，3，5，9，11，10，12。 (请注意，node环境下的事件监听依赖libuv与前端环境不完全相同，输出顺序可能会有误差)

# 参考文章
- [【THE LAST TIME】彻底吃透 JavaScript 执行机制](https://juejin.cn/post/6844903955286196237#comment)
- [这一次，彻底弄懂 JavaScript 执行机制](https://juejin.cn/post/6844903512845860872#comment)
- [深入理解 JavaScript Event Loop](https://zhuanlan.zhihu.com/p/34229323)