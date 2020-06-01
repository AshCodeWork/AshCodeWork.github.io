---
title: Typescript系列课程(一)
date: 2020-05-26 23:21:17
tags: 
    - Typescript
categories: Typescript
toc: true
---

## 本次主要讲的内容：
* 基础类型
  * 布尔值
  * 数字
  * 字符串
  * 数组
  * Any
  * Void
  * Null 和 Undefined
  * Object
  * 元祖Tuple
  * 枚举
  * Never
* 类型断言
* 接口
  * 一个例子
  * 可选属性
  * 只读属性
  * 额外属性检查
  * 函数类型
  * 可索引的类型
  * 类类型
  * 继承接口
  * 混合类型
  * 接口继承类

快速上手：[点我跳转](https://www.tslang.cn/docs/handbook/typescript-in-5-minutes.html)

Typescript文档地址：[点我跳转](https://www.tslang.cn/docs/handbook/basic-types.html)

练习地址：[点我跳转](https://www.tslang.cn/play/index.html)

<!--more-->

## 基础类型

### 布尔值
```ts
let isDone: boolean = false;
```

### 数字

```ts
let decLiteral: number = 6;
```

### 字符串

```ts
let name: string = "bob";
name = "smith";
```
模板字符串

```ts
let name: string = `Gene`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ name }.

I'll be ${ age + 1 } years old next month.`;
```
输出：
```
Hello, my name is Gene.

I'll be 38 years old next month.
```

### 数组

方式一：直接指定类型
```ts
//例如：定义一个数字类型的数组
let list: number[] = [1, 2, 3];
```
方式二：使用泛型
```ts
//使用数组泛型，Array<元素类型>
// 定义一个数字的
let list: Array<number> = [1, 2, 3]; 
// 定义一个字符串的
let list: Array<string> = ['a','b','c'];
```

### Any
有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 any类型来标记这些变量：

总结：就是你也不知道什么类型用any

```ts
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; //这样也是可以定义的，因为没有固定类型
```
在对现有代码进行改写的时候，`any`类型是十分有用的，它允许你在编译时可选择地包含或移除类型检查。 你可能认为 `Object`有相似的作用，就像它在其它语言中那样。 但是 `Object`类型的变量只是允许你给它赋任意值 - 但是却不能够在它上面调用任意的方法，即便它真的有这些方法：
```ts
let notSure: any = 4;
notSure.ifItExists(); // ok，ifItExists 这个方法可能在执行时存在
notSure.toFixed(); // ok, toFixed存在 (但是编译器不会检查)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
```

### Void
某种程度上来说，void类型像是与any类型相反，它表示没有任何类型。 **当一个函数没有返回值时，你通常会见到其返回值类型是 `void`**：

```ts
function warnUser(): void {
    console.log("这是一条警告消息");
}
//这个方法没有返回值
```
### Null 和 Undefined

```ts
//我们不能再给这些变量赋值
let u: undefined = undefined;
let n: null = null;

u=123 //报错
```
### Object

object表示非原始类型，也就是除number，string，boolean，[symbol](https://www.jianshu.com/p/165f5c2a10e9)，null或undefined之外的类型

```ts
// 声明一个create方法 参数为object或null 没有返回值
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
create(); //Error
```

### 元祖Tuple

元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为 string和number类型的元组。
```ts
// Declare a tuple type
let x: [string, number];

x = ['hello', 10]; // OK

x = [10, 'hello']; // Error
```
当访问一个已知索引的元素，会得到正确的类型：

```ts
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
```
当访问一个越界的元素，会使用[联合类型](https://www.tslang.cn/docs/handbook/advanced-types.html)替代：
```ts
x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型, 因为之前的两个类型是number类型和string类型

console.log(x[5].toString()); // OK, 'string' 和 'number' 都有 toString

x[6] = true; // Error, 布尔不是(string | number)类型
```

### 枚举

`enum`类型是对JavaScript标准数据类型的一个补充。 像C#等其它语言一样，使用枚举类型可以为一组数值赋予友好的名字。
```ts
enum Color {Red, Green, Blue}
let c: Color = Color.Green; //1
```
默认情况下，从0开始为元素编号。 你也可以手动的指定成员的数值。 例如，我们将上面的例子改成从 1开始编号：

```ts
enum Color {Red = 1, Green, Blue}
let c: Color = Color.Green; //2
```
或者，全部都采用手动赋值：
```ts
enum Color {Red = 1, Green = 2, Blue = 4}
let c: Color = Color.Green;
```

### Never

`never`类型表示的是那些永不存在的值的类型。

```ts
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```
## 类型断言

通过类型断言这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。

有两种写法：

写法一：尖括号
```ts
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
```
写法二：`as`（推荐）

```ts
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```
当你在TypeScript里使用JSX时，只有 `as`语法断言是被允许的。

## 接口

在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。

### 基本使用

举个例子：

```ts
function printLabel(labelledObj: { label: string }) {
  console.log(labelledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```
类型检查器会查看`printLabel`的调用。 `printLabel`有一个参数，并要求这个对象参数有一个名为`label`类型为`string`的属性。

这个时候我们就可以使用接口写法代替上面的写法

基本接口写法：
```ts
interface 接口名 {
  属性名: 类型;
}
```
注意： 如果使用这个接口的话，不能传入额外的属性，如果需要传入额外的属性，参考后面的**[额外属性检查](./#额外属性检查)**

通过接口写法我们可以把上面的例子改成下面这样：

```ts
// 定义一个名为LabelledValue的接口
interface LabelledValue {
  label: string;
}
// 定义printLabel
function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj); //Size 10 Objec
```
### 可选属性

可选属性的好处之一是可以对可能存在的属性进行预定义，好处之二是可以捕获引用了不存在的属性时的错误。

写法：在属性名后面增加一个`？`

```ts
interface 接口名 {
  属性名?: 类型;
}
```

```ts
// 定一个一个可选属性接口
interface SquareConfig {
  color?: string;
  width?: number;
}
// 创建一个正方形 传入颜色和宽度，返回颜色和面积
function createSquare(config: SquareConfig): {color: string; area: number} {
    // 默认 白色 100
    let newSquare = {color: "white", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

let mySquare = createSquare({color: "black"}); //{ area: 100, color: 'black' }

mySquare = createSquare({color: "black", width: 2});//{ area: 4, color: 'black' }
```

### 只读属性

一些对象属性只能在对象刚刚创建的时候修改其值

写法：属性名前加`readonly`
```ts
interface 接口名 {
  readonly 属性名: 类型;
}
```
```ts
interface Point {
    readonly x: number;
    readonly y: number;
}
// 定义一个p1变量，使用point接口
let p1: Point = { x: 10, y: 20 };
//修改x的值
p1.x = 5; // error!
```
#### 设置只读数组

TypeScript具有`ReadonlyArray<T>`类型，它与`Array<T>`相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改：

```ts
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
console.log(ro[0]) // ok，可以读取
ro[0] = 12; // error!
ro.push(5); // error!
ro.length = 100; // error!
a = ro; // error!
```
上面代码的最后一行，可以看到就算把整个ReadonlyArray赋值到一个普通数组也是不可以的。

**想要改成普通类型，只能使用类型断言重写**

```ts
a = ro as number[];
```
#### `readonly` vs `const`
最简单判断该用`readonly`还是`const`的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用 `const`，若做为属性则使用`readonly`。

### 额外属性检查

先看一个例子：
```ts
// 定义一个可选属性的接口
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): void {
    console.log(config.color) //undefined
    console.log(config.width) // 100
}
//这里我们假设打错了一个属性color -> colour
let mySquare = createSquare({ colour: "red", width: 100 });
```
这里程序会认为你设置的属性不对，ts会给colour有个红线，即便是可选属性。

我们有三个方案可以绕开这个检测：

方案一：使用断言
```ts
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);
```
方案二：重新赋值

```ts
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```
方案三：使用额外属性写法

```ts
//如果 SquareConfig带有上面定义的类型的color和width属性，并且还会带有任意数量的其它属性，那么我们可以这样定义它
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

### 函数类型

定义一个函数类型的接口

```ts
interface 接口名 {
    (参数1: 类型, 参数2: 类型): 返回值类型
}
```
举个例子

```ts
// 定义一个接口，接受两个参数：字符串的source 和 字符串的subString 结果返回布尔类型
interface SearchFunc {
  (source: string, subString: string): boolean;
}
// 设置接口类型
let mySearch: SearchFunc;
// 定义方法
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}
```
对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配。函数的参数会逐个进行检查，要求对应位置上的参数类型是兼容的。 比如，我们使用下面的代码重写上面的例子：

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
// 这里参数名称变成src 和 sub了
mySearch = function(src: string, sub: string): boolean {
  let result = src.search(sub);
  return result > -1;
}
```
如果你不想指定类型，TypeScript的类型系统会推断出参数类型

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    return result > -1;
}
//因为函数直接赋值给了 SearchFunc类型变量。 函数的返回值类型是通过其返回值推断出来的（此例是 false和true）。 如果让这个函数返回数字或字符串，类型检查器会警告我们函数的返回值类型与 SearchFunc接口中的定义不匹配。
```

### 可索引的类型

与使用接口描述函数类型差不多，我们也可以描述那些能够“通过索引得到”的类型，比如`a[10]`或`ageMap["daniel"]`。 可索引类型具有一个 **索引签名**，它描述了对象索引的类型，还有相应的索引返回值类型。

TypeScript支持两种索引签名：字符串和数字

数字索引签名
```ts
interface StringArray {
  [index: number]: string; //数字索引签名
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```
字符串索引签名

```ts
interface Animal {
    [name: string]: string //字符串索引签名
}

let dog: Animal = {
    name: 'dog', //只能设置值为string类型
    age: 44 //error 不能设置值为数字
}
```

### 类类型

#### 实现接口
TypeScript也能够用它来明确的强制一个类去符合某种契约。

注：接口描述了类的公共部分，而不是公共和私有两部分。 它不会帮你检查类是否具有某些私有成员。

```ts
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date); //描述一个方法站在类里实现它
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```
#### 类静态部分与实例部分的区别

当你操作类和接口的时候，你要知道类是具有两个类型的：静态部分的类型和实例的类型。

```ts
//用于构造函数的
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
// 用于实例方法的
interface ClockInterface {
    tick();
}
// 工厂函数
function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}
// 定义一个数字钟
class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
// 定义一个模拟时钟
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}
// 调用工厂函数实例化
let digital = createClock(DigitalClock, 12, 17);

let analog = createClock(AnalogClock, 7, 32);
```

### 继承接口

和类一样，接口也可以相互继承。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。

```ts
interface Shape {
    color: string;
}
// 继承Shape接口形成一个新的接口
interface Square extends Shape {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```
一个接口可以继承多个接口，创建出多个接口的合成接口。

```ts
interface 新的接口 extends 接口1, 接口2 {
  新增的属性: 类型
}
```
例子：

```ts
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}
// 继承多个接口
interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

### 混合类型

一个对象可以同时做为函数和对象使用，并带有额外的属性。

```ts
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { console.log(start) };
    counter.interval = 123;
    counter.reset = function () { console.log('reset') };
    return counter;
}
// 生成的c是一个函数，并且有额外属性
let c = getCounter();
c(10); // 10
c.reset(); //reset
c.interval = 5.0; // 5
```

### 接口继承类

```ts
class Control {
    private state: any;
}
// 接口继承类
interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control implements SelectableControl {
    select() { }
}

class TextBox extends Control {
    select() { }
}

// 错误：“Image”类型缺少“state”属性。
// 不行的原因就在于类必须要继承一次才能再使用接口实现
class Image implements SelectableControl {
    select() { }
}

class Location {

}
```