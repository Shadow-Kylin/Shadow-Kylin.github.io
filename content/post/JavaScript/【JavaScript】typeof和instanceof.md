---
title: 【JavaScript】typeof和instanceof
description: 
slug: 
date: 2023-10-10
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
从单词释义上看，type of——某某的类型，instance of——某某的实例。

从作用上看，typeof返回右值的数据类型的字符串表示。如下代码展示常见类型及typeof的对应输出：

```javascript
console.log(typeof 520) 					//"number"
console.log(typeof "1314") 					//"string"
console.log(typeof true) 					//"boolean"
console.log(typeof undefined) 				//"undefined"
console.log(typeof {}) 					    //"object"
console.log(typeof function(){}) 			//"function"
console.log(typeof Symbol("ShadowKylin")) 	 //"symbol"
console.log(typeof null); 					//"object"
```

> typeof对于原始值很有用，但不能有效区分数组、对象。

instanceof用于检查对象是否属于某个类的实例，换言之，是否是某个构造函数的实例，再换言之，是否在原型链上继承自该构造函数的原型。如下面代码所示：

```javascript
class Person {}
const person=new Person()

console.log(person instanceof Person)	//true
console.log(person instanceof Object)	//true，Object是所有对象的基类
```

那就要想了，~~能不能用typeof判断实例，或者用instanceof判断类型呢~~？当然都不能了。实例是对象，对象类型就是object；instanceof要求左值为对象，所以只能判断对象，`1 instanceof Number`结果是false。