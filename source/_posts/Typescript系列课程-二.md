---
title: Typescript系列课程(二)
date: 2020-05-29 17:28:25
tags: 
    - Typescript
categories: Typescript
toc: true
---

## 本次主要讲的内容：
* 类
  * 类
  * 继承
  * 公共，私有与受保护的修饰符
    * 公共修饰符 public
    * 私有修饰符 private
    * 保护修饰符 protected
    * 只读修饰符 readonly
  * 存取器
  * 静态属性
  * 抽象类
  * 高级技巧
    *  构造函数
    *  把类当做接口使用
* 函数
  * 函数
  * 函数类型
    * 为函数定义类型
    * 书写完整函数类型
  * 可选参数和默认参数
  * 剩余参数
  * this
    * this参数
    * this参数在回调函数里
  * 重载 
* 泛型  
  * 泛型之Hello World
  * 使用泛型变量
  * 泛型类型
  * 泛型类
  * 泛型约束
    * 在泛型约束中使用类型参数
    * 在泛型里使用类类型

快速上手：[点我跳转](https://www.tslang.cn/docs/handbook/typescript-in-5-minutes.html)

Typescript文档地址：[点我跳转](https://www.tslang.cn/docs/handbook/basic-types.html)

练习地址：[点我跳转](https://www.tslang.cn/play/index.html)

<!--more-->

## 类

传统的JavaScript程序使用函数和基于原型的继承来创建可重用的组件，但对于熟悉使用面向对象方式的程序员来讲就有些棘手，因为他们用的是基于类的继承并且对象是由类构建出来的。 从ECMAScript 2015，也就是ECMAScript 6开始，JavaScript程序员将能够使用基于类的面向对象的方式。 使用TypeScript，我们允许开发者现在就使用这些特性，并且编译后的JavaScript可以在所有主流浏览器和平台上运行，而不需要等到下个JavaScript版本。

### 类

一个使用类的例子：
```ts
// 定义一个Greeter的类
class Greeter {
    // 属性
    greeting: string;
    // new 的时候运行
    constructor(message: string) {
        this.greeting = message;
    }
    // 方法
    greet() {
        return "Hello, " + this.greeting;
    }
}
// 实例化
let greeter = new Greeter("world");
```
我们声明一个 `Greeter`类。这个类有3个成员：一个叫做 `greeting`的属性，一个构造函数和一个`greet`方法。

你会注意到，我们在引用任何一个类成员的时候都用了`this`。 它表示我们访问的是类的成员。

最后一行，我们使用`new`构造了`Greeter`类的一个实例。 它会调用之前定义的构造函数，创建一个`Greeter`类型的新对象，并执行构造函数初始化它。

### 继承

一个例子：
```ts
// 定一个animal类
class Animal {
    // 有一个移动方法
    move(distanceInMeters: number = 0) {
        console.log(`Animal moved ${distanceInMeters}m.`);
    }
}
// 定义一个dog类继承animal类
class Dog extends Animal {
    // 定一个犬吠的方法
    bark() {
        console.log('Woof! Woof!');
    }
}
// 实例化
const dog = new Dog();
dog.bark();
dog.move(10);
dog.bark();
```
这个例子展示了最基本的继承：类从基类中继承了属性和方法。 这里， `Dog`是一个 派生类，它派生自`Animal`基类，通过 `extends`关键字。 派生类通常被称作*子类*，基类通常被称作 *超类*。

因为`Dog`继承了`Animal`的功能，因此我们可以创建一个`Dog`的实例，它能够`bark()`和`move()`。

```ts
// 我们使用 extends关键字创建了 Animal的两个子类： Horse和 Snake。
// 超类 Animal
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
// 子类 Snake 继承 Animal
class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

### 公共，私有与受保护的修饰符

在上面的例子里，我们可以自由的访问程序里定义的成员。在TypeScript里，成员都默认为 public。

#### 公共修饰符 public

写法：
```ts
class 类名 {
    public 属性名: 类型
}
//或
class 类名 {
    属性名: 类型
}
```

public表示这个属性是可见的公有属性

```ts
class Animal {
    public name: string;
    public constructor(theName: string) { this.name = theName; }
    public move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
let dog = new Animal('柯基')
dog.move(10) // 柯基 moved 10m.
```
#### 私有修饰符 private

写法：
```ts
class 类名 {
    private 属性名: 类型
}
```

当成员被标记成 private时，它就不能在声明它的类的外部访问。

```ts
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("Cat").name; // 错误: 'name' 是私有的.
```
TypeScript使用的是结构性类型系统。 当我们比较两种不同的类型时，并不在乎它们从何处而来，如果所有成员的类型都是兼容的，我们就认为它们的类型是兼容的。

然而，当我们比较带有 `private`或 `protected`成员的类型的时候，情况就不同了。 如果其中一个类型里包含一个 `private`成员，那么只有当另外一个类型中也存在这样一个 `private`成员， 并且它们都是来自同一处声明时，我们才认为这两个类型是兼容的。 对于 `protected`成员也使用这个规则。

```ts
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat"); //constructor: ƒ Animal(theName)
let rhino = new Rhino();
let employee = new Employee("Bob"); //ƒ Employee(theName)

animal = rhino; //因为Rhino是Animal的子类所以可以兼容
animal = employee; // 错误: Animal 与 Employee 不兼容.
```
`Rhino`是`Animal`的子类，所以它继承了`Animal`的私有属性。因为 `Animal`和 `Rhino`共享了来自`Animal`里的私有成员定义 `private name: string`，因此它们是兼容的。然而`employee`来自`Employee`，并不继承自`Animal`，所以即便有相同名称的私有属性`name`也是不可以的。


#### 保护修饰符 protected

写法：
```ts
class 类名 {
    protected 属性名: 类型
}
```

`protected`修饰符与 private修饰符的行为很相似，但有一点不同， `protected`成员在派生类中仍然可以访问。例如：

```ts
class Person {
    protected name: string;
    constructor(name: string) { this.name = name; }
}

class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name)
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch()); //由于Employee是派生自Person的，所以可以访问通过实例方法访问protected属性name
console.log(howard.name); // 错误 name是被保护的，只能在Person和subClasses中有权限访问
```
构造函数也可以被标记成 `protected`。 这意味着这个类不能在包含它的类外被实例化，但是能被继承。比如，

```ts
class Person {
    protected name: string;
    protected constructor(theName: string) { this.name = theName; } //把构造函数设置成protected
}

// Employee 能够继承 Person
class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
let john = new Person("John"); // 错误: 'Person' 的构造函数是被保护的.
```

#### 只读修饰符 readonly

你可以使用 `readonly`关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

写法：
```ts
class 类名 {
    readonly 属性名: 类型
}
```
例子：

```ts
// 定义了一个章鱼class
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // 错误! name 是只读的.
```
#### 参数属性

```ts
class 类名 {
    constructor (readonly 属性名: 类型) {

    }
}
```
参数属性可以方便地让我们在一个地方定义并初始化一个成员。 下面的例子是对之前 `Octopus`类的修改版，使用了参数属性：

```ts
class Octopus {
    readonly numberOfLegs: number = 8;
    constructor(readonly name: string) {
    } //设置一个只读参数name
}
let dad = new Octopus("Man with the 8 strong legs");
console.log(dad.name) //Man with the 8 strong legs
```
注意看我们是如何舍弃了 `theName`，仅在构造函数里使用 `readonly name: string`参数来创建和初始化 `name`成员。 我们把声明和赋值合并至一处

### 存取器 getters/setters

TypeScript支持通过getters/setters来截取对对象成员的访问。 它能帮助你有效的控制对对象成员的访问。

 首先，我们从一个没有使用存取器的例子开始。
```ts
class Employee {
    fullName: string;
}

let employee = new Employee();
employee.fullName = "Bob Smith"; //设置名字
if (employee.fullName) {
    console.log(employee.fullName); //Bob Smith
}
```
下面这个版本里，我们先检查用户密码是否正确，然后再允许其修改员工信息。 我们把对`fullName`的直接访问改成了可以检查密码的 `set`方法。 我们也加了一个 `get`方法，让上面的例子仍然可以工作。

```ts
let passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName); // Bob Smith
}

// 如果我们在这里修改了 passcode变量，则不会弹出alert而是打印 Error: Unauthorized update of employee!
```
对于存取器有下面几点需要注意的：

首先，存取器要求你将编译器设置为输出ECMAScript 5或更高。 不支持降级到ECMAScript 3。 其次，只带有 `get`不带有 `set`的存取器自动被推断为 `readonly`。 这在从代码生成 `.d.ts`文件时是有帮助的，因为利用这个属性的用户会看到不允许够改变它的值。

### 静态属性 static

到目前为止，我们只讨论了类的实例成员，那些仅当类被实例化的时候才会被初始化的属性。 我们也可以创建类的静态成员，这些属性存在于类本身上面而不是类的实例上。

写法
```ts
class 类名 {
    static 属性 = 属性值
}
```
例子：

```ts
// 定义一个栅格的类
class Grid {
    static origin = {x: 0, y: 0}; //定一个静态属性orign
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        let xDist = (point.x - Grid.origin.x); //origin只能通过Gride访问，不能用this否则会报错
        let yDist = (point.y - Grid.origin.y);//origin只能通过Gride访问，不能用this否则会报错
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale; //Math.sqrt() 函数返回一个数的平方根，
    }
    constructor (public scale: number) { }
}

let grid1 = new Grid(1.0);  // 1x scale
let grid2 = new Grid(5.0);  // 5x scale

console.log(grid1.calculateDistanceFromOrigin({x: 10, y: 10})); //14.142135623730951
console.log(grid2.calculateDistanceFromOrigin({x: 10, y: 10})); //2.8284271247461903
```
在这个例子里，我们使用 `static`定义 `origin`，因为它是所有网格都会用到的属性。 每个实例想要访问这个属性的时候，都要在 `origin`前面加上类名。 如同在实例属性上使用 `this.`前缀来访问属性一样，这里我们使用 `Grid.`来访问静态属性。


### 抽象类 abstract

抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。 `abstract`关键字是用于定义抽象类和在抽象类内部定义抽象方法。

写法：
```ts
abstract class 类名 {
    abstract 方法(参数类型):返回类型
}
```

例子：
```ts
abstract class Animal {
    abstract makeSound(): void; //抽象方法，只描述不实现 必须在派生类中实现
    move(): void {
        console.log('roaming the earch...');
    }
}
```
抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。 抽象方法的语法与接口方法相似。 两者都是定义方法签名但不包含方法体。 然而，抽象方法必须包含 `abstract`关键字并且可以包含访问修饰符。

有几个规则:
1. 抽象类可以直接赋值，允许创建一个对抽象类型的引用
2. 不能创建一个抽象类的实例
3. 允许对一个抽象子类进行实例化和赋值
4. 在抽象子类中定义但在抽象类中没有声明的方法实例化后不能使用
5. 抽象类中的abstract定义的方法必须在派生类中实现
6. 在派生类的构造函数中必须调用 `super()`

```ts
abstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log('Department name: ' + this.name);
    }

    abstract printMeeting(): void; // 必须在派生类中实现
}

class AccountingDepartment extends Department {

    constructor() {
        super('Accounting and Auditing'); // 在派生类的构造函数中必须调用 super()
    }

    printMeeting(): void {
        console.log('The Accounting Department meets each Monday at 10am.');
    }

    generateReports(): void {
        console.log('Generating accounting reports...');
    }
}

let department: Department; // 允许创建一个对抽象类型的引用
department = new Department(); // 错误: 不能创建一个抽象类的实例
department = new AccountingDepartment(); // 允许对一个抽象子类进行实例化和赋值
department.printName();
department.printMeeting();
department.generateReports(); // 错误: 方法在声明的抽象类中不存在
```
### 高级技巧
#### 构造函数

当你在TypeScript里声明了一个类的时候，实际上同时声明了很多东西
1. 类的*实例的类型*。
2. 构造函数的值。
3. 类具有 *实例部分*与 *静态部分*这两个部分

```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter: Greeter; //Greeter类的实例的类型是 Greeter
greeter = new Greeter("world");
console.log(greeter.greet()); //Hello, world
```
#### 把类当做接口使用

类定义会创建两个东西：类的实例类型和一个构造函数。 因为类可以创建出类型，所以你能够在允许使用接口的地方使用类。

```ts
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3};
```


## 函数

TypeScript为JavaScript函数添加了额外的功能，让我们可以更容易地使用。

### 函数类型
#### 为函数定义类型

```ts
// 带函数名的函数
function add(x: number, y: number): number {
    return x + y;
}
// 赋值的匿名函数
let myAdd = function(x: number, y: number): number { return x + y; };
```

我们可以给每个参数添加类型之后再为函数本身添加返回值类型。 TypeScript能够根据返回语句自动推断出返回值类型，因此我们通常省略它。

```ts
// 不带返回值的写法
let myAdd = function(x: number, y: number){ return x + y; };
```

#### 书写完整函数类型

写法：

```ts
(参数名: 类型, 参数名: 类型) => 返回值类型
```
例子：

```ts
// 这个写法和上面为函数定义类型的写法效果实际上是一样的
let myAdd: (x: number, y: number) => number =
    function(x: number, y: number): number { return x + y; };
```
只要参数类型是匹配的，那么就认为它是有效的函数类型，而不在乎参数名是否正确。
```ts
// baseValue -> x  increment-> y
let myAdd: (baseValue: number, increment: number) => number =
    function(x: number, y: number): number { return x + y; };
```
### 可选参数和默认参数

传递给一个函数的参数个数必须与函数期望的参数个数一致。如果有的参数是可传可不传的则使用可选参数

#### 可选参数

写法：
```ts
function (参数? : 类型) {
    //...
}
```
例子：

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

let result1 = buildName("Bob");  //ok
let result2 = buildName("Bob", "Adams", "Sr.");  // error, 参数多了
let result3 = buildName("Bob", "Adams");  // ok
```
#### 默认值

可以给函数参数设置默认值，就跟es6一样

写法：
```ts
function (参数=值) {
    //...
}
```
例子：

```ts
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob"); // ok, returns "Bob Smith"
let result2 = buildName("Bob", undefined);// ok, returns "Bob Smith"
let result3 = buildName("Bob", "Adams", "Sr.");  // error, 参数多了
let result4 = buildName("Bob", "Adams"); // ok
```
### 剩余参数

```ts
function buildName(firstName: string, ...restOfName: string[]) {
    // 收集剩余参数到restOfName里
  return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie"); //Joseph Samuel Lucas MacKinzie
```
### this
#### this参数

```ts
// 定义一张牌的接口
interface Card {
    suit: string;
    card: number;
}
// 定义一副牌的接口
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}
// 设置一副牌
let deck: Deck = {
    suits: ["红桃", "黑桃", "梅花", "方块"], //花色
    cards: Array(52), //牌
    // 注意:该函数现在显式地指定它的被调用方必须是Deck类型，指定this类型为Deck
    createCardPicker: function(this: Deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker(); //抽到排

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

#### this参数在回调函数里

当你将一个函数传递到某个库函数里稍后会被调用时，你可能会看到this报错

```ts
interface UIElement {
    addClickListener(onclick: (this: void, e: Event) => void): void;
}

let uiElement:UIElement
class Handler {
    info: string;
    onClickBad(this: Handler, e: Event) {
        // oops, used this here. using this callback would crash at runtime
        this.info = e.message;
    }
}
let h = new Handler();
uiElement.addClickListener(h.onClickBad); // error! 认为这个this的类型不匹配
```
修改

```ts
interface UIElement {
    addClickListener(onclick: (this: void, e: Event) => void): void;
}

let uiElement:UIElement
class Handler {
    info: string;
    onClickBad = (e: Event) => {
        // 改为箭头函数即可，否则必须传入this为void类型
        this.info = e.message;
    }
}
let h = new Handler();
uiElement.addClickListener(h.onClickBad);
```

### 重载 

为同一个函数提供多个函数类型定义来进行函数重载。 编译器会根据这个列表去处理函数的调用。

举个例子：有个方法，传入数组，则从数组中抽一张牌。如果传入一个数字，则返回这个数字对应的花色和牌面数字。

下面这个例子没有使用重载
```ts
let suits = ["红桃", "黑桃", "梅花", "方块"];

function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "方块", card: 2 }, { suit: "黑桃", card: 10 }, { suit: "红桃", card: 4 }];
// 在调用方法的时候不会给有类型提示
let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);
// 在调用方法的时候不会给有类型提示
let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```
使用重载：

```ts
let suits = ["红桃", "黑桃", "梅花", "方块"];

function pickCard(x: {suit: string; card: number; }[]): number; //重载，如果传入一个牌的对象，则返回值张牌在牌堆里的下标
function pickCard(x: number): {suit: string; card: number; }; //重载，如果传入一个数字，则返回一张对应花色和数字的牌
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "方块", card: 2 }, { suit: "黑桃", card: 10 }, { suit: "红桃", card: 4 }];
// 在调用方法的时候会给有类型提示 
/*
function pickCard(x: {
    suit: string;
    card: number;
}[]): number (+1 overload)
*/
let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);
// 在调用方法的时候会给有类型提示
/*
function pickCard(x: number): {
    suit: string;
    card: number;
} (+1 overload)
*/
let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

## 泛型  

在像C#和Java这样的语言中，可以使用泛型来创建可重用的组件，一个组件可以支持多种类型的数据。 这样用户就可以以自己的数据类型来使用组件。

### 泛型之Hello World

```ts
// 假设我们想使用数字类型
function identity(arg: number): number {
    return arg;
}
```

### 使用泛型变量
### 泛型类型
### 泛型类
### 泛型约束
#### 在泛型约束中使用类型参数
#### 在泛型里使用类类型