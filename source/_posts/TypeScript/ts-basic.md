---
title: "TS 基础"
date: 2024-03-26 15:48:17
categories:
- TypeScript
tags:
- TypeScript
toc: true # 是否显示目录
---

> TS 基础
<!-- more -->
## 原始类型: string，number 和 boolean（The primitives）
`JavaScript` 有三个非常常用的原始类型 (opens new window)：`string`，`number` 和 `boolean`，每一个类型在 `TypeScript` 中都有对应的类型。他们的名字跟你在 JavaScript 中使用 `typeof` 操作符得到的结果是一样的。   
* `string` 表示字符串，比如 "Hello, world"
* `number` 表示数字，比如 42，JavaScript 中没有 int 或者 float，所有的数字，类型都是 number
* `boolean` 表示布尔值，其实也就两个值： true 和 false

## 数组（Array）
声明一个类似于 `[1, 2, 3]` 的数组类型，你需要用到语法 `number[]`。这个语法可以适用于任何类型（举个例子，`string[]` 表示一个字符串数组）。你也可能看到这种写法 `Array<number>`，是一样的。我们会在泛型章节为大家介绍 `T<U>` 语法。

> 注意 `[number]` 和 `number[]` 表示不同的意思，参考元组 (opens new window)章节

## any
TypeScript 有一个特殊的类型，any，当你不希望一个值导致类型检查错误的时候，就可以设置为 any 。

当一个值是 any 类型的时候，你可以获取它的任意属性 (也会被转为 any 类型)，或者像函数一样调用它，把它赋值给一个任意类型的值，或者把任意类型的值赋值给它，再或者是其他语法正确的操作，都可以：
```ts
let obj: any = { x: 0 };
// None of the following lines of code will throw compiler errors.
// Using `any` disables all further type checking, and it is assumed 
// you know the environment better than TypeScript.
obj.foo();
obj();
obj.bar = 100;
obj = "hello";
const n: number = obj;
```
当你不想写一个长长的类型代码，仅仅想让 TypeScript 知道某段特定的代码是没有问题的，any 类型是很有用的。

## 变量上的类型注解（Type Annotations on Variables）
当你使用 const、var 或 let 声明一个变量时，你可以选择性的添加一个类型注解，显式指定变量的类型：
```ts
let myName: string = "Alice";
```
`TypeScript` 并不使用“在左边进行类型声明”的形式，比如 `int x = 0；`类型注解往往跟在要被声明类型的内容后面。   
不过大部分时候，这不是必须的。因为 `TypeScript` 会自动推断类型。举个例子，变量的类型可以基于初始值进行推断：
```ts
// No type annotation needed -- 'myName' inferred as type 'string'
let myName = "Alice";
```
大部分时候，你不需要学习推断的规则。如果你刚开始使用，尝试尽可能少的使用类型注解。你也许会惊讶于，TypeScript 仅仅需要很少的内容就可以完全理解将要发生的事情。

## 函数（Function）

### 参数/返回值类型注解
```ts
function getFavoriteNumber(arg: number): number {
  return arg+1
}
```
### 匿名函数
匿名函数有一点不同于函数声明，当 TypeScript 知道一个匿名函数将被怎样调用的时候，匿名函数的参数会被自动的指定类型。  
这是一个例子：
```ts
// No type annotations here, but TypeScript can spot the bug
const names = ["Alice", "Bob", "Eve"];
// Contextual typing for function
names.forEach(function (s) {
  console.log(s.toUppercase());
  // Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
});
 
// Contextual typing also applies to arrow functions
names.forEach((s) => {
  console.log(s.toUppercase());
  // Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
});
```
尽管参数 `s` 并没有添加类型注解，但 TypeScript 根据 `forEach` 函数的类型，以及传入的数组的类型，最后推断出了 s 的类型。

这个过程被称为<b>上下文推断（contextual typing）</b>，因为正是从函数出现的上下文中推断出了它应该有的类型。

跟推断规则一样，你也不需要学习它是如何发生的，只要知道，它确实存在并帮助你省掉某些并不需要的注解。后面，我们还会看到更多这样的例子，了解一个值出现的上下文是如何影响它的类型的。

## 对象类型
```ts
// The parameter's type annotation is an object type
function printCoord(pt: { x: number; y?: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7 });
```
这里，我们给参数添加了一个类型，该类型有两个属性, `x` 和 `y`，两个都是 `number` 类型。你可以使用 , 或者 ; 分开属性，最后一个属性的分隔符加不加都行。

每个属性对应的类型是可选的，如果你不指定，默认使用 `any` 类型。

## 联合类型
第一种组合类型的方式是使用联合类型，一个联合类型是由两个或者更多类型组成的类型，表示值可能是这些类型中的任意一个。这其中每个类型都是联合类型的成员（members）。

让我们写一个函数，用来处理字符串或者数字：
```ts
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}
// OK
printId(101);
// OK
printId("202");
// Error
printId({ myID: 22342 });
// Argument of type '{ myID: number; }' is not assignable to parameter of type 'string | number'.
// Type '{ myID: number; }' is not assignable to type 'number'.
```

## 类型别名
我们已经学会在类型注解里直接使用对象类型和联合类型，这很方便，但有的时候，一个类型会被使用多次，此时我们更希望通过一个单独的名字来引用它。

这就是`类型别名（type alias）`。所谓`类型别名`，顾名思义，一个可以指代任意类型的名字。类型别名的语法是：
```ts
type Point = {
  x: number;
  y: number;
};
// Exactly the same as the earlier example
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 100, y: 100 });
```
你可以使用类型别名给任意类型一个名字，举个例子，命名一个联合类型：
```ts
type ID = number | string;
```
## 接口（Interfaces）
```ts
interface Point {
  x: number;
  y: number;
}
 
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```
就像我们在上节使用的类型别名，这个例子也同样可以运行，就跟我们使用了一个匿名对象类型一样。`TypeScript` 只关心传递给 `printCoord` 的值的结构（structure）——关心值是否有期望的属性。正是这种只关心类型的结构和能力的特性，我们才认为 `TypeScript` 是一个结构化（structurally）的类型系统。

### 类型别名和接口的不同
类型别名和接口非常相似，大部分时候，你可以任意选择使用。接口的几乎所有特性都可以在 `type` 中使用，两者最关键的差别在于类型别名本身无法添加新的属性，而接口是可以扩展的。
```ts
// Interface
// 通过继承扩展类型
interface Animal {
  name: string
}

interface Bear extends Animal {
  honey: boolean
}

const bear = getBear() 
bear.name
bear.honey
        
// Type
// 通过交集扩展类型
type Animal = {
  name: string
}

type Bear = Animal & { 
  honey: boolean 
}

const bear = getBear();
bear.name;
bear.honey;
```
```ts
// Interface
// 对一个已经存在的接口添加新的字段
interface Window {
  title: string
}

interface Window {
  ts: TypeScriptAPI
}

const src = 'const a = "Hello World"';
window.ts.transpileModule(src, {});
        
// Type
// 创建后不能被改变
type Window = {
  title: string
}

type Window = {
  ts: TypeScriptAPI
}

// Error: Duplicate identifier 'Window'.
```
在后续的章节里，你还会了解的更多。所以下面这些内容不能立刻理解也没有关系：  
* 在 `TypeScript 4.2` 以前，`类型别名`的名字可能会出现在报错信息中 (opens new window)，有时会替代等价的匿名类型（也许并不是期望的）。`接口`的名字则会始终出现在错误信息中。
* 类型别名也许不会实现声明合并，但是接口可以(opens new window)
* 接口可能只会被用于声明对象的形状，不能重命名原始类型(opens new window)
* 接口通过名字使用的时候，他们的名字会总是出现在错误信息中，如果直接使用，则会出现原始结构(opens new window)   
大部分时候，你可以根据个人喜好进行选择。`TypeScript` 会告诉你它是否需要其他方式的声明。如果你喜欢探索性的使用，那就使用 `interface` ，直到你需要用到 `type` 的特性。

## 类型断言（Type Assertions）
有的时候，你知道一个值的类型，但 `TypeScript` 不知道。  
举个例子，如果你使用 `document.getElementById`，`TypeScript` 仅仅知道它会返回一个 `HTMLElement`，但是你却知道，你要获取的是一个 `HTMLCanvasElement`。  
这时，你可以使用类型断言将其指定为一个更具体的类型：
```ts
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
```
就像`类型注解`一样，类型断言也会被编译器移除，并且不会影响任何运行时的行为。  
你也可以使用尖括号语法（注意不能在 `.tsx` 文件内使用），是等价的：
```ts
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```
> 谨记：因为类型断言会在编译的时候被移除，所以运行时并不会有类型断言的检查，即使类型断言是错误的，也不会有异常或者 `null` 产生。  
`TypeScript` 仅仅允许类型断言转换为一个更加具体或者更不具体的类型。这个规则可以阻止一些不可能的强制类型转换，比如：
```ts
const x = "hello" as number;
// Conversion of type 'string' to type 'number' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first.
```
有的时候，这条规则会显得非常保守，阻止了你原本有效的类型转换。如果发生了这种事情，你可以使用双重断言，先断言为 `any` （或者是 unknown），然后再断言为期望的类型：
```ts
const a = (expr as any) as T;
```
## 字面量类型（Literal Types）
除了常见的类型 `string` 和 `number` ，我们也可以将类型声明为更具体的数字或者字符串。  
众所周知，在 `JavaScript` 中，有多种方式可以声明变量。比如 `var` 和 `let` ，这种方式声明的变量后续可以被修改，还有 `const` ，这种方式声明的变量则不能被修改，这就会影响 `TypeScript` 为字面量创建类型。
```ts
let changingString = "Hello World";
changingString = "Olá Mundo";
// Because `changingString` can represent any possible string, that
// is how TypeScript describes it in the type system
changingString;
// let changingString: string
复制代码
const constantString = "Hello World";
// Because `constantString` can only represent 1 possible string, it
// has a literal type representation
constantString;
// const constantString: "Hello World"
```
字面量类型本身并没有什么太大用：
```ts
let x: "hello" = "hello";
// OK
x = "hello";
// ...
x = "howdy";
// Type '"howdy"' is not assignable to type '"hello"'.
```
如果结合联合类型，就显得有用多了。举个例子，当函数只能传入一些固定的字符串时：
```ts
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
printText("Hello, world", "left");
printText("G'day, mate", "centre");
// Argument of type '"centre"' is not assignable to parameter of type '"left" | "right" | "center"'.
```
数字字面量类型也是一样的：
```ts
function compare(a: string, b: string): -1 | 0 | 1 {
  return a === b ? 0 : a > b ? 1 : -1;
}
```
当然了，你也可以跟非字面量类型联合：
```ts
interface Options {
  width: number;
}
function configure(x: Options | "auto") {
  // ...
}
configure({ width: 100 });
configure("auto");
configure("automatic");

// Argument of type '"automatic"' is not assignable to parameter of type 'Options | "auto"'.
```
还有一种字面量类型，布尔字面量。因为只有两种布尔字面量类型， `true 和 false` ，类型 `boolean` 实际上就是联合类型 `true | false` 的别名。

### 字面量推断（Literal Inference）
当你初始化变量为一个对象的时候，TypeScript 会假设这个对象的属性的值未来会被修改，举个例子，如果你写下这样的代码：
```ts
const obj = { counter: 0 };
if (someCondition) {
  obj.counter = 1;
}
```
TypeScript 并不会认为 `obj.counter` 之前是 0， 现在被赋值为 1 是一个错误。换句话说，`obj.counter` 必须是 `number` 类型，但不要求一定是 0，因为类型可以决定读写行为。  
这也同样应用于字符串:
```ts
declare function handleRequest(url: string, method: "GET" | "POST"): void;
const req = { url: "https://example.com", method: "GET" };
handleRequest(req.url, req.method);
// Argument of type 'string' is not assignable to parameter of type '"GET" | "POST"'.
```
在上面这个例子里，`req.method` 被推断为 `string` ，而不是 `"GET"`，因为在创建 `req` 和 调用 `handleRequest` 函数之间，可能还有其他的代码，或许会将 `req.method` 赋值一个新字符串比如 `"Guess"` 。所以 TypeScript 就报错了。  
有两种方式可以解决：  
添加一个类型断言改变推断结果：
```ts
// Change 1:
const req = { url: "https://example.com", method: "GET" as "GET" };
// Change 2
handleRequest(req.url, req.method as "GET");
```
修改 1 表示“我有意让 `req.method` 的类型为字面量类型 `"GET"`，这会阻止未来可能赋值为 `"GUESS"` 等字段”。修改 2 表示“我知道 `req.method` 的值是 `"GET"`”. 
你也可以使用 `as const` 把整个对象转为一个类型字面量：
```ts
const req = { url: "https://example.com", method: "GET" } as const;
handleRequest(req.url, req.method);
```
`as const` 效果跟 `const` 类似，但是对类型系统而言，它可以确保所有的属性都被赋予一个字面量类型，而不是一个更通用的类型比如 `string 或者 number` 。

## null 和 undefined
`JavaScript` 两个原始类型的值，用于表示空缺或者未初始化，他们分别是 `null 和 undefined` 。  
`TypeScript` 有两个对应的同名类型。它们的行为取决于是否打开了 `strictNullChecks` (opens new window)选项。
### strictNullChecks 关闭
当 `strictNullChecks` (opens new window)选项关闭的时候，如果一个值可能是 `null 或者 undefined`，它依然可以被正确的访问，或者被赋值给任意类型的属性。这有点类似于没有空值检查的语言 (比如 C# ，Java) 。这些检查的缺少，是导致 bug 的主要源头，所以我们始终推荐开发者开启 strictNullChecks (opens new window)选项。
### strictNullChecks 打开
当 `strictNullChecks` (opens new window)选项打开的时候，如果一个值可能是 `null 或者 undefined`，你需要在用它的方法或者属性之前，先检查这些值，就像用可选的属性之前，先检查一下 是否是 `undefined` ，我们也可以使用<b>类型收窄</b>（narrowing）检查值是否是 `null`：
```ts
function doSomething(x: string | null) {
  if (x === null) {
    // do nothing
  } else {
    console.log("Hello, " + x.toUpperCase());
  }
}
```
## 非空断言操作符（后缀 !）
`TypeScript` 提供了一个特殊的语法，可以在不做任何检查的情况下，从类型中移除 `null 和 undefined`，这就是在任意表达式后面写上 `!` ，这是一个有效的类型断言，表示它的值不可能是 `null 或者 undefined`：
```ts
function liveDangerously(x?: number | null) {
  // No error
  console.log(x!.toFixed());
}
```
就像其他的类型断言，这也不会更改任何运行时的行为。重要的事情说一遍，只有当你明确的知道这个值不可能是 `null 或者 undefined` 时才使用 `!` 。

## 枚举（Enums）
枚举是 TypeScript 添加的新特性，用于描述一个值可能是多个常量中的一个。不同于大部分的 TypeScript 特性，这并不是一个类型层面的增量，而是会添加到语言和运行时。因为如此，你应该了解下这个特性。但是可以等一等再用，除非你确定要使用它。你可以在
[枚举类型 (opens new window)](https://www.typescriptlang.org/docs/handbook/enums.html)页面了解更多的信息。
```ts
enum Direction {
  Up = 1,
  Down,
  Left,
  Right,
}
```
## 不常见的原始类型
我们提一下在 `JavaScript` 中剩余的一些原始类型。但是我们并不会深入讲解。
### bigInt
ES2020 引入原始类型 BigInt，用于表示非常大的整数：
```ts
// Creating a bigint via the BigInt function
const oneHundred: bigint = BigInt(100);
 
// Creating a BigInt via the literal syntax
const anotherHundred: bigint = 100n;
```
你可以在 [TypeScript 3.2 的发布日志](the TypeScript 3.2 release notes)中了解更多信息。
### symbol
这也是 `JavaScript` 中的一个原始类型，通过函数 `Symbol()`，我们可以创建一个全局唯一的引用：
```ts
const firstName = Symbol("name");
const secondName = Symbol("name");
 
if (firstName === secondName) {
  // This condition will always return 'false' since the types 'typeof firstName' and 'typeof secondName' have no overlap.
  // Can't ever happen
}
```
你可以在 `Symbol` 页面 (opens new window)了解更多的信息。


