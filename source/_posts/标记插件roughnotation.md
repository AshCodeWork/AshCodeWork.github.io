---
title: 标记插件rough-notation
date: 2020-06-03 13:49:10
tags: 
    - rough-notation
    - 插件
    - 标记
categories: 插件
toc: true
---
{% asset_img introduce.png introduce %}

插件地址：https://roughnotation.com/

通过它的官网可以查看标记的效果。

<!--more-->

一个用于在web页面上创建和动画注释的小型JavaScript库。

Rough Notation使用RoughJS创建手绘外观。元素可以用许多不同的样式进行注释。动画持续时间和延迟可以配置，或只是关闭。

gzip压缩时大概占3.3kb。

[点击跳转官网的源码](https://github.com/pshihn/rough-notation-web)

[github地址](https://github.com/pshihn/rough-notation)

## 安装

npm

```node
npm install --save rough-notation
```
ES module

```ts
<script type="module" src="https://unpkg.com/rough-notation?module"></script>
```


IIFE 版本会创建 RoughNotation 对象在你的scope

```node
<script type="module" src="https://unpkg.com/rough-notation/lib/rough-notation.iife.js"></script>
```

## 使用

创建一个标记 annotation 对象，挂在在元素上，然后可以使用`show()`或`hide()`来控制

```
import { annotate } from 'rough-notation';

const e = document.querySelector('#myElement');
const annotation = annotate(e, { type: 'underline' });
annotation.show();
```

## 配置 Annotation

创建 Annotation 对象时，传入一个配置。配置只有一个强制字段`type`。您可以灵活的对其进行配置。

### type

这是一个强制的字段，它规定了annotation的样式。下面列出了type的类型: 
* **underline**: 下划线
* box: 给元素画一个方块
* circle: 在元素上画一个圈
* highlight: 创建一个高亮马克笔标记
* strike-through: 此样式在元素周围绘制一个框。
* crossed-off: 此样式在元素周围绘制一个框。

样式图例：

{% asset_img underline.png underline %}
{% asset_img box.png box %}
{% asset_img circle.png circle %}
{% asset_img highlight.png highlight %}
{% asset_img strike-through.png strike-through %}
{% asset_img crossed-off.png crossed-off %}

### animate

Boolean | default: true | 控制是否开启动画

### animationDuration

Number | default: 800 | 单位是毫秒

### animationDelay

Number | default: 0 | 单位是毫秒

### color

颜色色值 | 控制颜色

```js
const a1 = annotate($('#groupSection h3'), { type: 'box', color: '#BF360C' });
```

### strokeWidth

Number | default: 1 | 宽度

### padding 

default：5
如果您希望指定不同的顶部、左侧、右侧、底部，您可以将该值设置为类似于CSS样式padidng [top, right, bottom, left]或[top & bottom, left & right]的数组。

```js
const a4 = annotate($('header span.acircle'), { type: 'circle', color: '#F44336', padding: 2 });
```

## Annotation Object

|方法名| 功能 |
|:-:|:-:|
|isShowing() | 返回是否正在显示
|show() | 显示并开始执行动画
|hide()| 隐藏，没有动画
| remove() | 移除元素和标记的关联

示例：

```js
const annotate = RoughNotation.annotate;
const annotationGroup = RoughNotation.annotationGroup;

const config = { type: 'underline', strokeWidth: 3, padding: 3, color: '#B71C1C' };
const a1 = annotate($('#underlineSection h3'), config); // annotate Object
const a2 = annotate($('#underlineSection span'), config); // annotate Object
$('#underlineSection button').addEventListener('click', () => {
    a1.show(); //显示
    a2.show(); //显示
});
```

### Annotation Group Object

当您调用`annotationGroup`函数时，您将返回一个`annotation group`对象，该对象具有以下方法:

|方法名| 功能 |
|:-:|:-:|
|show()|按顺序绘制所有注释。如果注释被设置为animate(默认)，它将使绘图具有动画效果。如果再次调用，它将重新绘制动画。
|hide() | 如果显示，则隐藏所有标记。这不是动画。
