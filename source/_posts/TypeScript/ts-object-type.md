---
title: "TS 对象类型"
date: 2024-03-21 14:17:20
categories:
- TypeScript
tags:
- TypeScript
toc: true # 是否显示目录
---

> 对象类型
<!-- more -->
## 对象类型
在 `JavaScript` 中，最基本的将数据成组和分发的方式就是通过对象。在 TypeScript 中，我们通过对象类型 `（object types）` 来描述对象。    
对象类型可以是匿名的：
```ts
function greet(person: { name: string; age: number }) {
  return "Hello " + person.name;
}
```
也可以使用接口进行定义：
```ts
interface Person = {
  name: string;
  age: number;

}
function greet(person: Person) {
  return "Hello " + person.name;
}
```
或者通过类型别名：
```ts
type Person = {
  name: string;
  age: number
}
function greet(person: Person) {
  return "Hello " + person.name;
}
```

## 属性修饰符（Property Modifiers）
### 可选属性
对象类型中的每个属性可以说明它的类型、属性是否可选、属性是否只读等信息。
```ts
interface PaintOptions {
  shape: Shape;
  xPos?: number; // 可选属性
  yPos?: number;
}
 
function paintShape(opts: PaintOptions) {
  // ...
}
```
在这个例子中，xPos 和 yPos 就是可选属性。因为他们是可选的，所以上面所有的调用方式都是合法的。  
我们也可以尝试读取这些属性，但如果我们是在 `strictNullChecks` 模式下 `(tsconfig文件中，strictNullChecks默认为false)` ，TypeScript 会提示我们，属性值可能是 `undefined`。
```ts
interface PaintOptions {
  shape: Shape;
  xPos?: number; // 可选属性
  yPos?: number;
}
function paintShape(opts: PaintOptions) {
  let xPos = opts.xPos;              
  // (property) PaintOptions.xPos?: number | undefined
  let yPos = opts.yPos;
  // (property) PaintOptions.yPos?: number | undefined
}

//  ===> 针对 undefined 特殊处理为：

function paintShape(opts: PaintOptions) {
  let xPos = opts.xPos === undefined ? 0 : opts.xPos;
  // let xPos: number
  let yPos = opts.yPos === undefined ? 0 : opts.yPos;
  // let yPos: number
}
```
这种判断在 JavaScript 中很常见，以至于提供了专门的语法糖：
```ts
function paintShape({ shape, xPos = 0, yPos = 0 }: PaintOptions) {
  console.log("x coordinate at", xPos); // (parameter) xPos: number
  console.log("y coordinate at", yPos); // (parameter) yPos: number
  // ...
}
```
### readonly 属性
在 TypeScript 中，属性可以被标记为 readonly，这不会改变任何运行时的行为，但在类型检查的时候，一个标记为 readonly的属性是不能被写入的。
```ts
interface SomeType {
  readonly prop: string;
}
function doSomething(obj: SomeType) {
  // We can read from 'obj.prop'.
  console.log(`prop has the value '${obj.prop}'.`);
  // But we can't re-assign it.
  obj.prop = "hello";
  // Cannot assign to 'prop' because it is a read-only property.
}
```
`readonly` 仅仅表明属性本身是不能被重新写入的，属性内的值可以重新写入：
```ts
interface Home {
  readonly resident: { name: string; age: number };
}
function visitForBirthday(home: Home) {
  // We can read and update properties from 'home.resident'.
  console.log(`Happy birthday ${home.resident.name}!`);
  home.resident.age++;
}
 
function evict(home: Home) {
  // But we can't write to the 'resident' property itself on a 'Home'.
  home.resident = {
  // Cannot assign to 'resident' because it is a read-only property.
    name: "Victor the Evictor",
    age: 42,
  };
}
```
### 索引签名
有的时候，你不能提前知道一个类型里的所有属性的名字，但是你知道这些值的特征。
```ts
interface StringArray {
  [index: number]: string;
} 
const myArray: StringArray = getStringArray();
const secondItem = myArray[1]; // const secondItem: string
```
这样，我们就有了一个具有索引签名的接口 `StringArray`，这个索引签名表示当一个 `StringArray` 类型的值使用 `number` 类型的值进行索引的时候，会返回一个 `string` 类型的值。

尽管字符串索引用来描述字典模式（dictionary pattern）非常的有效，但也会强制要求所有的属性要匹配索引签名的返回类型。这是因为一个声明类似于 `obj.property` 的字符串索引，跟 `obj["property"]`是一样的。在下面的例子中，name 的类型并不匹配字符串索引的类型，所以类型检查器会给出报错：
```ts
interface NumberDictionary {
  [index: string]: number;
  length: number; // ok
  name: string;
	// Property 'name' of type 'string' is not assignable to 'string' index type 'number'.
}

// ====> 修改为联合类型：
interface NumberOrStringDictionary {
  [index: string]: number | string;
  length: number; // ok, length is a number
  name: string; // ok, name is a string
}
```
最后，你也可以设置索引签名为 readonly：
```ts
interface ReadonlyStringArray {
  readonly [index: number]: string;
}
let myArray: ReadonlyStringArray = getReadOnlyStringArray();
myArray[2] = "Mallory";
// 因为是readonly，所以无法修改值
// Index signature in type 'ReadonlyStringArray' only permits reading.
```
## 属性继承（Extending Types）
对接口使用 `extends` 关键字允许我们有效的从其他声明过的类型中拷贝成员，并且随意添加新成员。接口也可以继承多个类型：
```ts
interface Colorful {
  color: string;
}
interface Circle {
  radius: number;
}
interface ColorfulCircle extends Colorful, Circle {}
const cc: ColorfulCircle = {
  color: "red",
  radius: 42,
};
```
## 交叉类型（Intersection Types）
TypeScript 也提供了名为交叉类型（Intersection types）的方法，用于合并已经存在的对象类型。交叉类型的定义需要用到 `&` 操作符：
```ts
interface Colorful {
  color: string;
}
interface Circle {
  radius: number;
}
type ColorfulCircle = Colorful & Circle;
```
这里，我们连结 `Colorful` 和 `Circle` 产生了一个新的类型，新类型拥有 `Colorful` 和 `Circle` 的所有成员。
```ts
function draw(circle: Colorful & Circle) {
  console.log(`Color was ${circle.color}`);
  console.log(`Radius was ${circle.radius}`);
}
// okay
draw({ color: "blue", radius: 42 });
// oops
draw({ color: "red", raidus: 42 });
// Argument of type '{ color: string; raidus: number; }' is not assignable to parameter of type 'Colorful & Circle'.
// Object literal may only specify known properties, but 'raidus' does not exist in type 'Colorful & Circle'. Did you mean to write 'radius'?
```
> 这两种方式在合并类型上看起来很相似，但实际上还是有很大的不同。最原则性的不同就是在于冲突怎么处理，这也是你决定选择那种方式的主要原因。

```ts
interface Colorful {
  color: string;
}
interface ColorfulSub extends Colorful {
  color: number
}
// Interface 'ColorfulSub' incorrectly extends interface 'Colorful'.
// Types of property 'color' are incompatible.
// Type 'number' is not assignable to type 'string'.

// 以上使用继承的方式，如果重写类型会导致编译错误，但交叉类型不会：

interface Colorful {
  color: string;
}
type ColorfulSub = Colorful & {
  color: number
}
// 虽然不会报错，那 color 属性的类型是什么呢，答案是 never，取得是 string 和 number 的交集。
```
## 泛型对象类型（Generic Object Types）
把 Box 想象成一个实际类型的模板，Type 就是一个占位符，可以被替代为具体的类型。当 TypeScript 看到 `Box<string>`，它就会替换为 `Box<Type>` 的 Type 为 string ，最后的结果就会变成 `{ contents: string }`。换句话说，`Box<string>` 和 `StringBox` 是一样的。
```ts
interface Box<Type> {
  contents: Type;
}
interface StringBox {
  contents: string;
}
 
let boxA: Box<string> = { contents: "hello" };
boxA.contents;        
// (property) Box<string>.contents: string
 
let boxB: StringBox = { contents: "world" };
boxB.contents;     
// (property) StringBox.contents: string
```
不过现在的 Box 是可重复使用的，如果我们需要一个新的类型，我们完全不需要再重新声明一个类型。
```ts
interface Box<Type> {
  contents: Type;
}
interface Apple {
  a: string
  // ....
}
// Same as '{ contents: Apple }'.
type AppleBox = Box<Apple>;

let new1 : AppleBox = {
  contents: {a: '1'}
}
```
这也意味着我们可以利用泛型函数避免使用函数重载:
```ts
function setContents<Type>(box: Box<Type>, newContents: Type) {
  box.contents = newContents;
}
```
类型别名不同于接口，可以描述的不止是对象类型，所以我们也可以用类型别名写一些其他种类的的泛型帮助类型:
```ts
type OrNull<Type> = Type | null;

type OneOrMany<Type> = Type | Type[];
 
type OneOrManyOrNull<Type> = OrNull<OneOrMany<Type>>;
           
type OneOrManyOrNull<Type> = OneOrMany<Type> | null
 
type OneOrManyOrNullStrings = OneOrManyOrNull<string>;
               
type OneOrManyOrNullStrings = OneOrMany<string> | null
```
### Array 类型
我们之前讲过 Array 类型，当我们这样写类型 `number[]` 或者 ` string[]` 的时候，其实它们只是 `Array<number>` 和 `Array<string>` 的简写形式而已。
```ts
function doSomething(value: Array<string>) {
  // ...
}
let myArray: string[] = ["hello", "world"];
// either of these work!
doSomething(myArray);
doSomething(new Array("hello", "world"));
```
类似于上面的 Box 类型，Array 本身就是一个泛型：
```ts
interface Array<Type> {
  /**
   * Gets or sets the length of the array.
   */
  length: number;
 
  /**
   * Removes the last element from an array and returns it.
   */
  pop(): Type | undefined;
 
  /**
   * Appends new elements to an array, and returns the new length of the array.
   */
  push(...items: Type[]): number;
 
  // ...
}
```
### ReadonlyArray 类型
ReadonlyArray 主要是用来做意图声明。当我们看到一个函数返回 ReadonlyArray，就是在告诉我们不能去更改其中的内容，当我们看到一个函数支持传入 ReadonlyArray ，这是在告诉我们我们可以放心的传入数组到函数中，而不用担心会改变数组的内容。
```ts
function doStuff(values: ReadonlyArray<string>) {
  // We can read from 'values'...
  const copy = values.slice();
  console.log(`The first value is ${values[0]}`);
 
  // ...but we can't mutate 'values'.
  values.push("hello!");
  // Property 'push' does not exist on type 'readonly string[]'.
}
```
TypeScript 也针对 `ReadonlyArray<Type>` 提供了更简短的写法 `readonly Type[]` :
```ts
function doStuff(values: readonly string[]) {
  // We can read from 'values'...
  const copy = values.slice();
  console.log(`The first value is ${values[0]}`);
 
  // ...but we can't mutate 'values'.
  values.push("hello!");
  // Property 'push' does not exist on type 'readonly string[]'.
}
```
### 元组类型
元组类型是另外一种 Array 类型，当你明确知道数组包含多少个元素，并且每个位置元素的类型都明确知道的时候，就适合使用元组类型。
```ts
type StringNumberPair = [string, number];
```
如果要获取元素数量之外的元素，TypeScript 会提示错误：
```ts
function doSomething(pair: [string, number]) {
  // ...
 
  const c = pair[2];
  // Tuple type '[string, number]' of length '2' has no element at index '2'.
}
```
我们也可以使用 JavaScript 的数组解构语法解构元组：
```ts
function doSomething(stringHash: [string, number]) {
  const [inputString, hash] = stringHash;
  console.log(inputString); // const inputString: string
  console.log(hash); // const hash: number
}
```
可选元素和剩余元素的存在，使得 TypeScript 可以在参数列表里使用元组，就像这样：
```ts
function readButtonInput(...args: [string, number, ...boolean[]]) {
  const [name, version, ...input] = args;
  // ...
}

// =====> 基本等同于：

function readButtonInput(name: string, version: number, ...input: boolean[]) {
  // ...
}
```
### readonly 元组类型
这样 TypeScript 就不会允许写入readonly 元组的任何属性：
```ts
function doSomething(pair: readonly [string, number]) {
  pair[0] = "hello!";
  // Cannot assign to '0' because it is a read-only property.
}
```
在大部分的代码中，元组只是被创建，使用完后也不会被修改，所以尽可能的将元组设置为 readonly 是一个好习惯。

如果我们给一个数组字面量 const 断言，也会被推断为 readonly 元组类型。
```ts
let point = [3, 4] as const;
function distanceFromOrigin([x, y]: [number, number]) {
  return Math.sqrt(x ** 2 + y ** 2); // 5
}
distanceFromOrigin(point);
// Argument of type 'readonly [3, 4]' is not assignable to parameter of type '[number, number]'.
// The type 'readonly [3, 4]' is 'readonly' and cannot be assigned to the mutable type '[number, number]'.
```
尽管 distanceFromOrigin 并没有更改传入的元素，但函数希望传入一个可变元组。因为 point 的类型被推断为 readonly [3, 4]，它跟 [number number] 并不兼容，所以 TypeScript 给了一个报错。


## 参考
* [TS 中文文档](https://ts.yayujs.com/handbook/ObjectTypes.html#%E5%AF%B9%E8%B1%A1%E7%B1%BB%E5%9E%8B-object-types)