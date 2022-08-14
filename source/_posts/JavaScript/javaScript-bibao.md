---
title: "理解JS 执行上下文 作用域 闭包"
date: 2022-03-12 12:22:54
categories:
- JavaScript
tags:
- JavaScript
toc: true # 是否显示目录
---

> 执行上下文
> 作用域
> 闭包

<!-- more -->
# 执行上下文([参考文章](https://juejin.cn/post/6844903682283143181#comment))
  ## 什么是执行上下文
  执行上下文是评估和执行 JavaScript 代码的环境的抽象概念。每当 Javascript 代码在运行的时候，它都是在执行上下文中运行。
  ## 三种执行上下文类型
  * 全局执行上下文 — 这是默认或者说基础的上下文，任何不在函数内部的代码都在全局上下文中。它会执行两件事：创建一个全局的 window 对象（浏览器的情况下），并且设置 this 的值等于这个全局对象。一个程序中只会有一个全局执行上下文。
  * 函数执行上下文 — 每当一个函数被调用时, 都会为该函数创建一个新的上下文。每个函数都有它自己的执行上下文，不过是在函数被调用时创建的。函数上下文可以有任意多个。每当一个新的执行上下文被创建，它会按定义的顺序（将在后文讨论）执行一系列步骤。
  * Eval 函数执行上下文 — 执行在 eval 函数内部的代码也会有它属于自己的执行上下文，但由于 JavaScript 开发者并不经常使用 eval，所以在这里不讨论它。 

  ## 创建执行上下文
  有两个阶段：1) 创建阶段 和 2) 执行阶段
  ### 创建阶段
  * this 值的决定，即我们所熟知的 This 绑定。
  * 创建词法环境组件。（存在差异，还需阅读源码）
  * 创建变量环境组件。

    this 绑定：
    在全局执行上下文中，this 的值指向全局对象。(在浏览器中，this引用 Window 对象)。
    在函数执行上下文中，this 的值取决于该函数是如何被调用的。如果它被一个引用对象调用，那么 this 会被设置成那个对象，否则 this 的值被设置为全局对象或者 undefined（在严格模式下）。例如：
    ```bash 
    let foo = {
      baz: function() {
      console.log(this);
      }
    }

    foo.baz();   // 'this' 引用 'foo', 因为 'baz' 被
                // 对象 'foo' 调用

    let bar = foo.baz;

    bar();       // 'this' 指向全局 window 对象，因为
                // 没有指定引用对象
    ```
  ### 执行阶段
  完成对所有这些变量的分配，最后执行代码。
  >  ⚠️注意：在执行阶段，如果 JavaScript 引擎不能在源码中声明的实际位置找到 let 变量的值，它会被赋值为 undefined。

# 执行栈
  ## 什么是执行栈
  是一种拥有 LIFO（后进先出）数据结构的栈，被用来存储代码运行时创建的所有执行上下文。当 JavaScript 引擎第一次遇到你的脚本时，它会创建一个全局的执行上下文并且压入当前执行栈。每当引擎遇到一个函数调用，它会为该函数创建一个新的执行上下文并压入栈的顶部。
  引擎会执行那些执行上下文位于栈顶的函数。当该函数执行结束时，执行上下文从栈中弹出，控制流程到达当前栈中的下一个上下文。
  ```bash
    let a = 'Hello World!';

    function first() {
      console.log('Inside first function');
      second();
      console.log('Again inside first function');
    }

    function second() {
      console.log('Inside second function');
    }

    first();
    console.log('Inside Global Execution Context');

  ```
  1. 当上述代码在浏览器加载时，JavaScript 引擎创建了一个全局执行上下文并把它压入当前执行栈。当遇到 first() 函数调用时，JavaScript 引擎为该函数创建一个新的执行上下文并把它压入当前执行栈的顶部。
  2. 当从 first() 函数内部调用 second() 函数时，JavaScript 引擎为 second() 函数创建了一个新的执行上下文并把它压入当前执行栈的顶部。当 second() 函数执行完毕，它的执行上下文会从当前栈弹出，并且控制流程到达下一个执行上下文，即 first() 函数的执行上下文。
  3. 当 first() 执行完毕，它的执行上下文从栈弹出，控制流程到达全局执行上下文。一旦所有代码执行完毕，JavaScript 引擎从当前栈中移除全局执行上下文。


# 基本概念
 - js的执行实际上是放到执行上下文栈的，

 - 执行上下文就是为代码的执行做了一系列准备，包括this和词法环境变量环境等等

 - 执行栈：存放执行上下文的地方，在栈顶的执行上下文就是当前执行上下文，js总会到这里寻找资源。函数执行上下文在执行完毕之后就会从栈顶弹出

 - 创建执行上下文的四种情况：进入全局代码，进入function函数体代码，进入eval函数指定的代码，进入module函数

 - 执行上下文的内部结构：词法环境【环境记录（申明，存放变量与函数的地方）+outer指向(形成作用域链的关键)】+ this绑定(这个就是我们熟悉的this指向)([参考文章](https://blog.csdn.net/Linshu__/article/details/121874961))

 - 这里我们把写在环境记录上的变量和函数统称为词法环境

 - 作用域就是解析（查找）变量名的一个集合，就是当前运行上下文（也可以是当前上下文的词法环境)
    - 全局作用域就是全局执行上下文
    - 函数作用域就是函数执行上下文


 # 全局执行上下文中的词法环境

  ```bash
  console.log(foo);
  if (true) {
    var foo = 'foo'
  }

  step1:创建执行上下文，并加入栈顶，当前执行上下文就是这个全局执行上下文，在栈顶
  tips:全局执行上下文的文本环境（花名册）有两部分组成：全局对象（浏览器执行环境就是window对象）以及全局[[scope]] (其实也是一个块作用域，全局的块)
  两者的区别：
  var和function声明创建在全局对象中，而let const class声明的变量创建在全局scope中
  先到全局scope中查找变量，然后再到全局对象中查找   
          
  step2:分析：
  找到所有非函数中的var声明
  找到所有的顶级函数声明
  找到let const class声明

  step3:名字重复处理
  1.let const class声明的名字之间不能重复
  2.let const class和var function的名字之间不能重复
  3.var 和 function名字重复的 function

  step4:创建绑定
  声明并初始化var为undefined
  顶级函数声明：申明function名字，并初始化为新创建对象（这里函数的对象就已经创建完成了）
  块级中函数声明（比如在if块中）：声明名字，初始化为undefined
  声明let const class 但未初始化,不能使用 所以会报错

  step5:执行语句
       
  ```

```bash
js中的代码总是遵循先申明再执行的准则，那么在申明的时候var function是申明在全局对象上的，而let const是登录在全局scope上的。var声明放入变量会提升并初始化为undefined,但是let const 也会提升(此处还需仔细探讨[let到底会不会造成变量提升](https://blog.csdn.net/amyleeYMY/article/details/122555195))，只是不会初始化，所以会报错。查找变量的时候总是从全局scope到全局对象上面查找

var a = 10
function  foo() {
    console.log(a);
    let a
}
foo()
// Uncaught ReferenceError: Cannot access 'a' before initialization

```
> [变量提升与函数提升的机制](https://segmentfault.com/a/1190000008568071)


> 块级作用域
  
  块级作用域,不会形成自己的执行上下文，链接在原来的文本环境之前
  执行完毕之后就会被销毁
  ```bash
  let inIf = 'out if statement'
  if (true) {
      let inIf = 'in if statement'
      console.log(inIf);  //in if statement
  }
  console.log(inIf);   //out if statement
  ```
  判断与我们之前找的var 顶级函数声明 let/const/class声明是否重复 如果重复了，就不做处理如果不重复，就在全局对象中创建一个以函数名命名的变量，并且将其初始化为undefined

  ```bash

  console.log(foo);  //undefined
  if (true) {
      function foo() {
          console.log('in block');   //in block
      }
  }
  foo()

  因为块里面是函数，在函数foo执行完毕销毁之前，它会查看全局变量中有没有函数名foo
  没有的话，不做处理
  有的话，就会把全局变量中foo的值(undefined)替换 全局对象中已经变成函数对象
  在块里的函数也是会提升的，只不过它没有立即创建函数，它是undefined

  console.log(foo);
  if (false) {
      // function foo() {
      //     console.log('......');
      // }
  }


  let foo;
  // 报错,let声明的变量在全局scope上，foo在全局对象上没有找到foo,于是不做处理
  var foo
  // ';;',var声明的变量在全局对象上，foo在全局对象上找到foo,于是在执行结束之前完成赋值
  if (true) {
      function foo() {
          console.log(';;');
      }
  }
  foo()
  先申明后使用，申明的地方就是执行上下文

  ```

> 函数作用域

 - function函数会创建在全局执行上下文的词法环境（全局对象）之中，函数创建的时候就已经完成初始化有函数名了。并且函数创建的时候就会携带一个[[scope]]（类似于一个小背包，记录函数创建的环境）所以所函数在创建的时候体内就保存了它创建时执行上下文的文本环境。等到它执行的时候，会自动找到它的[[scope]],里面保存着创建时的文本环境

- 函数调用的执行上下文——函数在哪里创建，就保存哪里的运行上下文；函数的作用域是在函数创建的时候决定的而不是调用的时候决定

- 函数的作用域链是根据函数创建的位置形成的作用域链，这就是所谓的静态作用域，词法作用域

> 函数
  - 函数创建
    JS声明函数的三种方式（[参考文章](https://blog.csdn.net/qq_30638831/article/details/88628421)）:
      // 函数表达式(function expression) 
      var h = function() {
            // h
      }

      // 函数声明(function declaration) 
      function h() {
            // h
      }
    1.函数声明: 即上面第二种方式,会声明一个具名函数, 且函数能在其所在作用域的任意位置被调用, 其创建的函数为具名函数, 证明这一
    点你可以 console.log(h.name); 可以看到打印为 “h”. 可在后面的代码中将此函数通过函数名赋值给变量或者对象属性

    2.函数表达式: 即上面第一种方式, 这种方法使用function操作符创建函数, 表达式可以存储在变量或者对象属性里. 函数表达式往往被称为
    匿名函数, 因为它没有名字. 证明这一点你可以 console.log(h.name); 可以看到打印为空 “”

    3.Function()构造器: 不推荐这种用法, 容易出问题

    1-函数声明

    ```bash
      关于函数声明，它最重要的一个特征就是函数声明提升，意思是执行代码之前先读取函数声明。不管函数声明写在前面，还是后面，都会出现函数声明的提升。
      add(1,2); //弹窗显示：3  
      function add(x,y){  
          alert(x+y) 
      }  
    ```
    2-命名函数表达式

    ```bash
      var add = function(x,y){  
          alert(x+y) 
      }
      add(1,2) //弹窗显示：3

    ```
    这种形式看起来好像是常规的变量赋值语句。但是函数表达式和函数声明的区别在于，函数表达式在使用前必须先赋值。所以这段代码执行的时候就会出错：
    ```bash
    add(1,2) //无弹窗，报错： add is not a function  
    var add = function(x,y){  
        alert(x+y)  
    } 

    ```
    造成这种现象是因为解析器在向执行环境中加载数据时，解析器会率先读取函数声明，并使其在执行任何代码前可用；至于函数表达式，则必须等到解析器执行到它的所在的的代码行，才会真正的被解析。函数表达式中，创建的函数叫做匿名函数，因为function关键字后面没有标识符。

    ```bash
    先说两者的显著区别:

       - 第一种声明方式也就是var声明方式, 函数只有在var语句声明之后才能被调用

       - 第二种生命方式也就是function声明方式, 函数可以在function声明之前被调用

      这是因为,
       - 对第一种情况, 函数表达式是在函数运行阶段才赋值给变量h

       - 对第二种情况, 函数申明是在代码运行阶段之前, 也就是代码解析阶段就赋值给标识符h

       可以看下面两个例子:
          var h = function () {
                // h
          }

          console.log(h)
              
          h = function () {
                // h1
          }
          打印： ƒ h() {
                  // h
                }
          因为赋值发生在代码运行阶段, 代码自上而下运行console.log(h)所在位置只能获取它之前的赋值

          第二种情况：
          function h() {
                // h
          }

          console.log(h)
              
          function h() {
                // h1
          }
           打印： ƒ h() {
                    // h1
                 }

          因为赋值发生在代码解析阶段, 代码运行到console.log(h)时解析早已完成, 而解析的结果是后面那个函数h, 故会打印此结果
    ```
    函数表达式（匿名函数）调用方式：
    
    使用()将匿名函数括起来，然后后面再加一对小括号（包含参数列表）。我们再看一下以下一个例子：
      ```bash
      //表达式的调用
      /*
      const add = function (x, y){
          return x + y;
      }
      const sum = add(1, 2)

      console.log(sum)
      */

      //匿名函数式调用
      //方式1
      //这种方式尽量少用
      const sum1 = function(x, y){
          return x + y;
      }(1, 2);

      //方式2 自执行函数
      //推荐
      const sum = (function(x, y){
          return x + y;
      })(1, 2);
      console.log(sum1);

      //方式3
      (new Function("x","y","return x+y"))(1,2)
      ```

      > 自执行函数，即定义和调用合为一体。我们创建了一个匿名的函数，并立即执行它，由于外部无法引用它内部的变量，因此在执行完后很快就会被释放，关键是这种机制不会污染全局对象。
      ``` bash
        // 下面2个括弧()都会立即执行  
        (function () { /* code */ } ()) // 推荐使用这个  
        (function () { /* code */ })() // 但是这个也是可以用的  
        
        // 由于括弧()和JS的&&，异或，逗号等操作符是在函数表达式和函数声明上消除歧义的  
        // 所以一旦解析器知道其中一个已经是表达式了，其它的也都默认为表达式了  
        var i = function () { return 10; } ();  
        true && function () { /* code */ } ();  
        0, function () { /* code */ } ();  
              
        // 如果你不在意返回值，或者不怕难以阅读
        // 你甚至可以在function前面加一元操作符号  
        !function () { /* code */ } ();  
        ~function () { /* code */ } ();  
        -function () { /* code */ } ();  
        +function () { /* code */ } ();  
          
        // 还有一个情况，使用new关键字,也可以用，但我不确定它的效率  
        // http://twitter.com/kuvos/status/18209252090847232  
        new function () { /* code */ }  
        new function () { /* code */ } () // 如果需要传递参数，只需要加上括弧() 
    ```
    
    函数声明的函数创建过程使用的是当前运行上下文的词法环境，但是命名函数表达式创建过程是在当前运行上下文词法环境之前加了新的词法环境，在自己的词法环境中添加对函数命名funname的绑定，并通过outer与当前运行上下文的词法环境链接起来。funname在函数外是没有定义的

    3-new Function创建
      - 只能访问全局变量，所以不管在哪儿创建都类似于在全局环境中创建


  - 函数的执行
    不同的函数调用方式会给函数传递不同的thisArg值
     - 普通函数包括IIDE：传递undefined
     - 对象方法：传递对象
     - new方法：传递新创建的对象
  - 不同调用方式的this指向
    this就是thisBinding，但是它是动态的，它与在哪儿调用密切相关

     - 普通函数调用：undefined(非严格模式下是window)
     - 作为方法调用：指向someObject
     - new function调用：总是指向新创建的newObject
     - call bind apply 指向参数对象
     - 箭头函数与在哪儿创建有关，不能使用call bind apply

# 闭包
  参考
  * [我从来不理解JavaScript闭包，直到有人这样向我解释它...](https://zhuanlan.zhihu.com/p/56490498)
  * [JS 闭包经典使用场景和含闭包必刷题](https://juejin.cn/post/6937469222251560990#comment)

  如果没有闭包的存在，函数执行完了之后，它的词法环境就会被销毁，正是有了闭包的存在，才能在全局环境中引用函数，继而保证整个函数环境的存在（函数体内总是携带着它初始化时的环境）。闭包可以让函数在运行完毕之后，其运行上下文的词法环境仍然能被访问。

  ``` bash
  function foo() {
      console.log(a);
  }
  function bar() {
      var a = 3
      foo()
  }
  var a = 2
  bar()   //2
  ```
