---
title: 【JavaScript】null和undefined
description: 
slug: 
date: 2023-10-10
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
# 前言

在 JavaScript 中，`null` 和 `undefined` 是两个特殊的值，它们表示着不同的含义，但在某些情况下会引起混淆。

# null

`null`是Null类型的唯一值，是一个假值，表示一个**空对象指针**，即不存在的对象，这也是我们使用`typeof`判断`null`时返回`Object`的原因。

```javascript
console.log(typeof null); //object
```

当你声明一个变量但不给它赋值时，它的默认值是 `undefined`，但你也可以明确将其赋值为 `null`，表示该变量的值为空。

`null` 通常用于明确指示一个变量没有值，或者用于重置对象的引用，以便后续垃圾回收。

## null 的判断

假值有很多，我们不能通过下面类型代码判断一个变量是不是`null`：

```javascript
if (!null) {
    console.log('null is false value')
}else{
    console.log('null is true value')
}
```

那怎么判断是不是`null`呢？

1.使用严格相等运算符 `===`

使用`===`全等号而不是等于号`==`，因为 javascript 认为`null==undefined`成立。

```javascript
const value=null
if(value===null){
    //值为null
}else{
    //值不为null
}
```

2.使用 `Object.is()`

`Object.is()` 用于比较两个值是否相等，与 `===` 行为类似，但它对于一些边界情况的处理更为人性化。

```javascript
console.log(NaN === NaN); // 输出 false
console.log(Object.is(NaN, NaN)); // 输出 true
console.log(-0 === 0); // 输出 true
console.log(Object.is(-0, 0)); // 输出 false
console.log(+0 === -0); // 输出 true
console.log(Object.is(+0, -0)); // 输出 false
var abc = null;
console.log(Object.is(abc, null)); //true
```

# undefined

`undefined`也是`Undefined`类型的唯一值，也是一个假值，在我们**声明变量但没初始化**（赋值）时，变量自动赋值为`undefined`。当然，值为`undefined`的已声明变量 和 未定义变量 还是有区别的，如下：

```javascript
let a;
console.log(a)	//undefined
console.log(b)	//未定义变量报错
```

此外，`undefined` 也表示一个对象属性不存在的情况。如果你尝试访问对象中不存在的属性，将返回 `undefined`。

如果函数的参数没有传递值，它们的默认值是 `undefined`，而不是 `null`。
## undefined 的判断

1.使用严格相等运算符 `===`

```javascript
var abc;
if (abc === undefined) {
    console.log("值为 undefined");
}
```

2.使用类型比较运算符 `typeof`

```javascript
var abc;
if (typeof abc === "undefined") {
    console.log("值为 undefined");
}
```

3.使用 `Object.is()`

```javascript
var abc;
if (Object.is(abc,undefined)) {
    console.log("值为 undefined");
}
```

有点特别的是，使用`typeof`判断值为`undefined`的已声明变量 和 未定义变量的结果都为`undefined`。这是由于两者在使用价值上是相同的，都是没有实际使用价值。

```javascript
var abc;
console.log(typeof abc); //undefined
console.log(typeof efg); //undefined
```

最后，使用方面，我们不必显示赋值为`undefined`，但可以在需要的时候赋值为`null`。