---
title: Typescript系列课程(三)
date: 2020-06-08 10:31:50
tags: 
    - Typescript
categories: Typescript
toc: true
---

## 本次主要讲的内容：
* 枚举
  * 数字枚举
  * 字符串枚举
  * 异构枚举（Heterogeneous enums）
  * 计算的和常量成员
  * 联合枚举与枚举成员的类型
  * 运行时的枚举
    *  反向映射
    *  const枚举
  * 外部枚举
* 类型推论
  * 基础
  * 最佳通用类型
  * 上下文类型
* 类型兼容性
  * 介绍
  * 开始
  * 比较两个函数
    * 函数参数双向协变
    * 可选参数及剩余参数
    * 函数重载
  * 枚举
  * 类
    * 类的私有成员和受保护成员
  * 泛型
* 高级类型（上）
  * 交叉类型（Intersection Types）
  * 联合类型（Union Types）
  * 类型保护与区分类型（Type Guards and Differentiating Types）
    * 用户自定义的类型保护
    * typeof类型保护
    * instanceof类型保护
  * 可以为null的类型
    * 可选参数和可选属性
    * 类型保护和类型断言


快速上手：[点我跳转](https://www.tslang.cn/docs/handbook/typescript-in-5-minutes.html)

Typescript文档地址：[点我跳转](https://www.tslang.cn/docs/handbook/basic-types.html)

练习地址：[点我跳转](https://www.tslang.cn/play/index.html)

<!--more-->

## 枚举

使用枚举我们可以定义一些带名字的常量。 使用枚举可以清晰地表达意图或创建一组有区别的用例。

### 数字枚举

数字枚举就是指枚举值都是数字。相当于定义一串key，key的值从上到下自增1。

写法： 

```ts
enum 枚举类型 {
    枚举属性1, //默认是0 如果需要设置默认值：枚举属性1=1设置枚举属性值从1开始
    枚举属性2,
    ...
}
```
例子：

```ts
enum Direction {
    Up = 1, //1
    Down, //2
    Left, //3
    Right //4
}
```
如上，我们定义了一个数字枚举， Up使用初始化为 1。 其余的成员会从 1开始自动增长。 换句话说， Direction.Up的值为 1， Down为 2， Left为 3， Right为 4。

```ts
enum Direction {
    Up, // 0
    Down, //1
    Left, //2
    Right, //3
}
```
现在， Up的值为 0， Down的值为 1等等。 当我们不在乎成员的值的时候，这种自增长的行为是很有用处的，但是要注意每个枚举成员的值都是不同的。

```ts
enum Values {
    No = 0,
    Yes
}

function respond(recipient: string, message: Values): void {
    // 打印传入的值
    console.log(recipient, message)
}

respond("Princess Caroline", Values.Yes) //Princess Caroline 1
```

### 字符串枚举

字符串是枚举值。定义的时候类似于定义对象。类似于数字枚举的写法，只不过传入的值为字符串。

```ts
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
alert(Direction.Up) //UP
alert(Direction.Down) //DOWN
alert(Direction.Left) //LEFT
alert(Direction.Right) //RIGHT
```
由于字符串枚举没有自增长的行为，字符串枚举可以很好的序列化。 换句话说，如果你正在调试并且必须要读一个数字枚举的运行时的值，这个值通常是很难读的 - 它并不能表达有用的信息（尽管 [反向映射](#反向映射) 会有所帮助），字符串枚举允许你提供一个运行时有意义的并且可读的值，独立于枚举成员的名字。

### 异构枚举（Heterogeneous enums）

就是混合使用数字和字符串作为枚举值。不建议使用

```ts
enum BooleanLikeHeterogeneousEnum {
    No = 0,
    Yes = "YES",
}
```
### 计算的和常量成员

每个枚举成员都带有一个值，它可以是 常量或 计算出来的。 当满足如下条件时，枚举成员被当作是常量：
* 它是枚举的第一个成员且没有初始化器，这种情况下它被赋予值 0
```ts
enum E { X }
// E.X 为常量0
```
* 它不带有初始化器且它之前的枚举成员是一个 数字常量。 这种情况下，当前枚举成员的值为它上一个枚举成员的值加1
```ts
enum E2 {
    A = 1, B, C
}
// E.A 为常量1
// E.B 为常量2
// E.C 为常量3
```
* 枚举成员使用**常量枚举表达式**初始化。 常数枚举表达式是TypeScript表达式的子集，它可以在编译阶段求值。 当一个表达式满足下面条件之一时，它就是一个常量枚举表达式：
  * 一个枚举表达式字面量（主要是字符串字面量或数字字面量)
  * 一个对之前定义的常量枚举成员的引用（可以是在不同的枚举类型中定义的）
  * 带括号的常量枚举表达式
  * 一元运算符 `+`, `-`,`~`其中之一应用在了常量枚举表达式
  * 常量枚举表达式做为二元运算符 `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `>>>`, `&`, `|`, `^`的操作对象。 若常数枚举表达式求值后为 NaN或 Infinity，则会在编译阶段报错。

例如：
```ts
enum E1 {
    B = 2
}
enum E2 {
    A = 1, //一个枚举表达式数字字面量
    B = E1.B, //一个对之前定义的常量E1枚举成员B的引用
    D = 1 << 1, //结果：2 数字1向左移动一位
    F = B | D //常量枚举表达式做为二元运算符|
}
console.log(E2.A) //1
console.log(E2.B) //2
console.log(E2.D) //2
console.log(E2.F) //2
```
  
所有其它情况的枚举成员被当作是需要计算得出的值。

```ts
enum FileAccess {
    // 常量成员
    None,
    Read    = 1 << 1,
    Write   = 1 << 2,
    ReadWrite  = Read | Write,
    // 计算成员
    G = "123".length
}
```

### 联合枚举与枚举成员的类型

首先，枚举成员成为了类型。我们可以说某些成员 只能是枚举成员的值：

```ts
enum ShapeKind {
    Circle,
    Square,
}

interface Circle {
    kind: ShapeKind.Circle;
    radius: number;
}

interface Square {
    kind: ShapeKind.Square;
    sideLength: number;
}

let c: Circle = {
    kind: ShapeKind.Square, //kind报错：只能是Circle中的kind，也就是说必须是ShapeKind的枚举值ShapeKind.Circle。因为限定了变量c的类型，而类型接口中确定了kind属性的值
    radius: 100,
}
```
另一个变化是枚举类型本身变成了每个枚举成员的 联合（在高级类型中会讲）。 因此，TypeScript能够捕获在比较值的时候犯的愚蠢的错误。 例如：

```ts
enum E {
    Foo,
    Bar,
}

function f(x: E) {
    if (x !== E.Foo || x !== E.Bar) {
        //             ~~~~~~~~~~~
        // Error! Operator '!==' cannot be applied to types 'E.Foo' and 'E.Bar'.这个条件将永远返回true，因为这两个值涵盖了枚举E所有情况
    }
}
```
这个例子里，我们先检查`x`是否不是 `E.Foo`。 如果通过了这个检查，然后 `||`会发生短路效果， `if`语句体里的内容会被执行。 然而，这个检查没有通过，那么 `x`则 只能为 `E.Foo`，因此没理由再去检查它是否为 `E.Bar`

### 运行时的枚举

枚举是在运行时真正存在的对象。它可以传递给函数。
```ts
enum E {
    X, Y, Z
}
function f(obj: { X: number }) {
    return obj.X;
}

// 可以，因为E有一个名为X的属性，它是一个数字。
f(E);
```
#### 反向映射

可以用枚举返回的值映射会枚举的key

```ts
enum Enum {
    A
}
let a = Enum.A;
let nameOfA = Enum[a]; // "A"
// 生成的代码中，枚举类型被编译成一个对象，它包含了正向映射（ name -> value）和反向映射（ value -> name）。 引用枚举成员总会生成为对属性访问并且永远也不会内联代码。
```
**注意：不会为字符串枚举成员生成反向映射**

#### `const`枚举

大多数情况下，枚举是十分有效的方案。 然而在某些情况下需求很严格。 为了避免在额外生成的代码上的开销和额外的非直接的对枚举成员的访问，我们可以使用 `const`枚举。 常量枚举通过在枚举上使用 `const`修饰符来定义。

```ts
const enum Enum {
    A = 1,
    B = A * 2
}
```
常量枚举只能使用常量枚举表达式，并且不同于常规的枚举，它们在编译阶段会被删除。简单说就是这种常量的枚举，在编译的时候会直接把枚举值放入对应使用的地方。

```ts
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]
```
编译后的代码为

```js
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

### 外部枚举

外部枚举用来描述已经存在的枚举类型的形状。外部枚举和非外部枚举之间有一个重要的区别，在正常的枚举里，没有初始化方法的成员被当成常数成员。 对于非常数的外部枚举而言，没有初始化方法时被当做需要经过计算的。

```ts
// 声明一个枚举类型，常用在声明文件中
declare enum Enum {
    A = 1,
    B, // 不带declare时会认为这个是常熟成员，此时它被认为是需要经过计算的
    C = 2
}
```

## 类型推论

即，类型是在哪里如何被推断的。

### 基础

TypeScript里，在有些没有明确指出类型的地方，类型推论会帮助提供类型。如下面的例子
```ts
let x = 3; //变量x的类型被推断为数字。 这种推断发生在初始化变量和成员，设置默认参数值和决定函数返回值时。
```

### 最佳通用类型

当需要从几个表达式中推断类型时候，会使用这些表达式的类型来推断出一个最合适的通用类型。例如

```ts
let x = [0, 1, null];
```
为了推断x的类型，我们必须考虑所有元素的类型。 这里有两种选择： `number`和`null`。 计算通用类型算法会考虑所有的候选类型，并给出一个兼容所有候选类型的类型。

```ts
let zoo = [new Rhino(), new Elephant(), new Snake()]; //推断为 (Rhino | Elephant | Snake)[]
```
这里，我们想让zoo被推断为Animal[]类型，但是这个数组里没有对象是Animal类型的，因此不能推断出这个结果。 为了更正，当候选类型不能使用的时候我们需要明确的指出类型：

```ts
let zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()]; //Animal[]
```

### 上下文类型

TypeScript类型推论也可能按照相反的方向进行。 这被叫做“按上下文归类”。按上下文归类会发生在表达式的类型与所处的位置相关时。

## 类型兼容性

我们使用了“兼容性”，它在语言规范里没有定义。 在TypeScript里，有两种兼容性：子类型和赋值。 它们的不同点在于，赋值扩展了子类型兼容性，增加了一些规则，允许和any来回赋值，以及enum和对应数字值之间的来回赋值。

语言里的不同地方分别使用了它们之中的机制。 实际上，类型兼容性是由赋值兼容性来控制的，即使在implements和extends语句也不例外。

### 介绍

在基于名义类型的类型系统中，数据类型的兼容性或等价性是通过明确的声明和/或类型的名称来决定的。这与结构性类型系统不同，它是基于类型的组成结构，且不要求明确地声明。用人话说就是typescript只在意你的类型组成的结果，不强制要求对class声明类类型。

```ts
interface Named {
    name: string;
}

class Person {
    name: string;
}

let p: Named;
// OK, 因为组成的结构类型中有name
p = new Person();
```

### 开始

TypeScript结构化类型系统的基本规则是，如果x要兼容y，那么y至少具有与x相同的属性。比如：

```ts
interface Named {
    name: string;
}

let x: Named;
// y's inferred type is { name: string; location: string; }
// 如果这里直接给x赋值下面这个字面量对象会报x的类型错误
let y = { name: 'Alice', location: 'Seattle' };
// 关键点：这里要检查y是否能赋值给x，编译器检查x中的每个属性，看是否能在y中也找到对应属性。 在这个例子中，y必须包含名字是name的string类型成员。y满足条件，因此赋值正确。
x = y;

// 检查函数参数时使用相同的规则：
function greet(n: Named) {
    // 注意，y有个额外的location属性，但这不会引发错误。 只有目标类型（这里是Named）的成员会被一一检查是否兼容。
    console.log('Hello, ' + n.name);
}
greet(y); // OK
```

### 比较两个函数

对来讲，在比较原始类型和对象类型的时候是比较容易理解的，问题是如何判断两个函数是兼容的。
```ts
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

/**
 * 要查看x是否能赋值给y，首先看它们的参数列表。 x的每个参数必须能在y里找到对应类型的参数。 注意的是参数的名字相同与否无所谓，只*看它们的类型。 这里，x的每个参数在y中都能找到对应的参数，所以允许赋值。
 **/
y = x; // OK

/**
 * 因为y有个必需的第二个参数，但是x并没有，所以不允许赋值。
 */
x = y; // Error
```
类型系统强制源函数的返回值类型必须是目标函数返回值类型的子类型。

#### 函数参数双向协变

当比较函数参数类型时，只有当源函数参数能够赋值给目标函数或者反过来时才能赋值成功。 这是不稳定的，因为调用者可能传入了一个具有更精确类型信息的函数，但是调用这个传入的函数的时候却使用了不是那么精确的类型信息。 实际上，这极少会发生错误，并且能够实现很多JavaScript里的常见模式。例如：

```ts
enum EventType { Mouse, Keyboard }

interface Event { timestamp: number; }
interface MouseEvent extends Event { X: number; Y: number }
interface KeyEvent extends Event { keyCode: number }

function listenEvent(eventType: EventType, handler: (n: Event) => void) {
    /* ... */
    let target = document.querySelector('body')
    target.addEventListener('click', handler)
}

// 不健全，但有用并且常见
listenEvent(EventType.Mouse, (e: MouseEvent) => console.log(e.x + ',' + e.y));

// 传入的是一个不准确的类型，但是使用的时候确是含有更多类型信息的类型。比如MouseEvent的类型信息是多于Event的
listenEvent(EventType.Mouse, (e: Event) => console.log((<MouseEvent>e).x + ',' + (<MouseEvent>e).y));
listenEvent(EventType.Mouse, <(e: Event) => void>((e: MouseEvent) => console.log(e.x + ',' + e.y)));

// 需要的是Event却传入的number
listenEvent(EventType.Mouse, (e: number) => console.log(e)); //Error
```
#### 可选参数及剩余参数

比较函数兼容性的时候，可选参数与必须参数是可互换的。 源类型上有额外的可选参数不是错误，目标类型的可选参数在源类型里没有对应的参数也不是错误。

剩余参数：当一个函数有剩余参数时，它被当做无限个可选参数。

可选参数：这对于类型系统来说是不稳定的，但从运行时的角度来看，可选参数一般来说是不强制的，因为对于大多数函数来说相当于传递了一些undefinded。

有一个好的例子，常见的函数接收一个回调函数并用对于程序员来说是可预知的参数但对类型系统来说是不确定的参数来调用：

```ts
function invokeLater(args: any[], callback: (...args: any[]) => void) {
    /* ... Invoke callback with 'args' ... */
    callback(args)
}

// 不可靠 - invokeLater "might" provide any number of arguments
invokeLater([1, 2], (x, y) => console.log(x + ', ' + y)); //1,2

// 让人困惑 (x and y are 事实上是需要的) and 非显示的
invokeLater([1, 2], (x?, y?) => console.log(x + ', ' + y)); //1,2
```

#### 函数重载

对于有重载的函数，源函数的每个重载都要在目标函数上找到对应的函数签名。 这确保了目标函数可以在所有源函数可调用的地方调用。

### 枚举

* 枚举类型与数字类型兼容
* 数字类型与枚举类型兼容
* 不同枚举类型之间是不兼容的
  
比如:

```ts
enum Status { Ready, Waiting };
enum Color { Red, Blue, Green };

let _status = Status.Ready;
let a:number = _status; //数字类型与枚举类型兼容
_status = Color.Green;  // Error 不同枚举类型之间是不兼容的
_status = 3; //数字类型与枚举类型兼容
```

### 类

类与对象字面量和接口差不多，但有一点不同：类有静态部分和实例部分的类型。 比较两个类类型的对象时，只有实例的成员会被比较。 静态成员和构造函数不在比较的范围内。

TS中类的静态部分指的是这个类（函数）本身，实例部分指的是类实例化出来的对象

```ts
class Animal {
    feet: number; //只检查feet
    constructor(name: string, numFeet: number) { } //不检查
}

class Size {
    feet: number; //只检查feet
    constructor(numFeet: number) { } //不检查
}

let a: Animal;
let s: Size;

a = s;  // OK
s = a;  // OK
```

#### 类的私有成员和受保护成员

类的私有成员和受保护成员会影响兼容性。 当检查类实例的兼容时，如果目标类型包含一个私有成员，那么源类型必须包含来自同一个类的这个私有成员。 同样地，这条规则也适用于包含受保护成员实例的类型检查。 这允许子类赋值给父类，但是不能赋值给其它有同样类型的类。

### 泛型

因为TypeScript是结构性的类型系统，类型参数只影响使用其做为类型一部分的结果类型。比如

```ts
interface Empty<T> {
}
let x: Empty<number>;
let y: Empty<string>;

x = y;  // OK, because y matches structure of x
```
上面代码里，x和y是兼容的，因为它们的结构使用类型参数时并没有什么不同。 把这个例子改变一下，增加一个成员，就能看出是如何工作的了：

```ts
interface NotEmpty<T> {
    data: T;
}
let x: NotEmpty<number>;
let y: NotEmpty<string>;

x = y;  // Error, x 和 y不兼容了
```
在这里，泛型类型在使用时就好比不是一个泛型类型。


## 高级类型
### 交叉类型（Intersection Types）

交叉类型是将多个类型合并为一个类型。 这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性。

写法：
```
类型1 & 类型2 & 类型3
```
例子：
```ts
// 定一个继承方法，可以将两个对象合并
// 这里定义了一个交叉类型 T & U
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U>{};
    for (let id in first) {
        (<any>result)[id] = (<any>first)[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            (<any>result)[id] = (<any>second)[id];
        }
    }
    return result;
}

class Person {
    constructor(public name: string) { }
}
interface Loggable {
    log(): void;
}
class ConsoleLogger implements Loggable {
    log() {
        // ...
    }
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();
```

### 联合类型（Union Types）

联合类型与交叉类型类似，表示一串类型的或关系。可以是这一串类型中的任何一个。

写法：
```ts
类型1 | 类型2 | 类型3
```
例如：

```ts
/**
 * 在字符串的左面放一个 "padding".
 * If 'padding' is a string, 就把传入的'padding'放到左边.
 * If 'padding' is a number, 就把number*空格数放到文字左面.
 */
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
padLeft("Hello world", "  "); // returns "  Hello world"
```


### 类型保护与区分类型（Type Guards and Differentiating Types）

我们像确切的知道哪一个类型怎么处理？

```ts
// 定义一个鸟的接口
interface Bird {
    fly();
    layEggs();
}
// 定义一个鱼的接口
interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors

// 可以使用断言处理
if ((<Fish>pet).swim) {
    (<Fish>pet).swim();
}
else {
    (<Bird>pet).fly();
}
```
#### 用户自定义的类型保护

假若我们一旦检查过类型，就能在之后的每个分支里清楚地知道 pet的类型的话就好了。我们可以使用**类型谓词**解决：

写法：
```ts
// parameterName必须是来自于当前函数签名里的一个参数名。
parameterName is Type
```

TypeScript里的 类型保护机制让它成为了现实。 类型保护就是一些表达式，它们会在运行时检查以确保在某个作用域里的类型。 要定义一个类型保护，我们只要简单地定义一个函数，它的返回值是一个 类型谓词：
```ts
function isFish(pet: Fish | Bird): pet is Fish {
    return (<Fish>pet).swim !== undefined;
}
```
在这个例子里， `pet is Fish`就是类型谓词。 谓词为` parameterName is Type`这种形式， `parameterName`必须是来自于当前函数签名里的一个参数名。

每当使用一些变量调用 `isFish`时，TypeScript会将变量缩减为那个具体的类型，只要这个类型与变量的原始类型是兼容的。
```ts
// 'swim' 和 'fly' 调用都没有问题了

if (isFish(pet)) {
    pet.swim();
}
else {
    pet.fly();
}
```

注意TypeScript不仅知道在 if分支里 pet是 Fish类型； 它还清楚在 else分支里，一定 不是 Fish类型，一定是 Bird类型。

#### typeof类型保护

同js中使用typeof一样，进行类型判断。但是只能判断"number"， "string"， "boolean"或 "symbol"

写法：

```ts
typeof v === "typename"
// 或
typeof v !== "typename"
// "typename"必须是 "number"， "string"， "boolean"或 "symbol"
```
#### instanceof类型保护

instanceof类型保护是通过构造函数来细化类型的一种方式。 

instanceof的右侧要求是一个构造函数，TypeScript将细化为：
1. 此构造函数的 prototype属性的类型，如果它的类型不为 any的话
2. 构造签名所返回的类型的联合

比如，我们借鉴一下之前字符串填充的例子：

```ts
// 定义一个接口
interface Padder {
    getPaddingString(): string
}
// 空格padder
class SpaceRepeatingPadder implements Padder {
    constructor(private numSpaces: number) { }
    getPaddingString() {
        return Array(this.numSpaces + 1).join(" ");
    }
}
// 字符串padder
class StringPadder implements Padder {
    constructor(private value: string) { }
    getPaddingString() {
        return this.value;
    }
}
// 随机选择使用空格padder还是字符串padder
function getRandomPadder() {
    return Math.random() < 0.5 ?
        new SpaceRepeatingPadder(4) :
        new StringPadder("  ");
}

// 类型为SpaceRepeatingPadder | StringPadder
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
    padder; // 类型细化为'SpaceRepeatingPadder'
}
if (padder instanceof StringPadder) {
    padder; // 类型细化为'StringPadder'
}
```
### 可以为null的类型

TypeScript具有两种特殊的类型， `null`和 `undefined`，它们分别具有值`null`和`undefined`. 我们在[基础类型](./Basic Types.md)一节里已经做过简要说明。 默认情况下，类型检查器认为 `null`与 `undefined`可以赋值给任何类型。 `null`与 `undefined`是所有其它类型的一个有效值。 

使用`--strictNullChecks`标记可以解决此错误：当你声明一个变量时，它不会自动地包含 `null`或 `undefined`。

```ts
let s = "foo";
s = null; // 错误, 'null'不能赋值给'string'
let sn: string | null = "bar";
sn = null; // 可以

sn = undefined; // error, 'undefined'不能赋值给'string | null'
```

#### 可选参数和可选属性

使用了 `--strictNullChecks`，可选参数会被自动地加上 `| undefined:`

```ts
function f(x: number, y?: number) {
    return x + (y || 0);
}
f(1, 2);
f(1);
f(1, undefined); //第二个参数是可选参数，在严格模式下会自动增加undefined类型
f(1, null); // error, 'null' is not assignable to 'number | undefined'
```
可选属性也会有同样的处理：

```ts
class C {
    a: number;
    b?: number; //b 是可选参数，在严格模式下会自动增加undefined类型
}
let c = new C();
c.a = 12;
c.a = undefined; // error, 'undefined' is not assignable to 'number'
c.b = 13;
c.b = undefined; // ok
c.b = null; // error, 'null' is not assignable to 'number | undefined'
```
#### 类型保护和类型断言

由于可以为`null`的类型是通过联合类型实现，那么你需要使用类型保护来去除 null。 
幸运地是这与在JavaScript里写的代码一致：

```ts
function f(sn: string | null): string {
    return sn || "default";
}
```
如果编译器不能够去除 null或 undefined，你可以使用类型断言手动去除。 

写法
```
需要去除的变量!
```
例如：

```ts
function broken(name: string | null): string {
  function postfix(epithet: string) {
    return name.charAt(0) + '.  the ' + epithet; // error, 'name' is possibly null
  }
  name = name || "Bob";
  return postfix("great");
}

function fixed(name: string | null): string {
  function postfix(epithet: string) {
    // 使用类型断言
    return name!.charAt(0) + '.  the ' + epithet; // ok
  }
  name = name || "Bob";
  return postfix("great");
}
```
