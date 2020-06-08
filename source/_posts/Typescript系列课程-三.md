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
* 高级类型
  * 交叉类型（Intersection Types）
  * 联合类型（Union Types）
  * 类型保护与区分类型（Type Guards and Differentiating Types）
    * 用户自定义的类型保护
    * typeof类型保护
    * instanceof类型保护
  * 可以为null的类型
    * 可选参数和可选属性
    * 类型保护和类型断言
  * 类型别名
    * 接口 vs. 类型别名
  * 字符串字面量类型
  * 数字字面量类型
  * 枚举成员类型
  * 可辨识联合（Discriminated Unions）
    * 完整性检查
  * 多态的 this类型
  * 索引类型（Index types）
    * 索引类型和字符串索引签名
  * 映射类型
    * 由映射类型进行推断


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
### 计算的和常量成员
### 联合枚举与枚举成员的类型
### 运行时的枚举
#### 反向映射
#### `const`枚举
### 外部枚举