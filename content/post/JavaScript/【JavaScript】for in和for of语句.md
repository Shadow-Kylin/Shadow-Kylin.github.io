---
title: 【JavaScript】for in和for of语句
description: 
slug: 
date: 2023-08-28
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
# for in

for in 语句以任意顺序遍历一个对象的**除Symbol以外的可枚举属性**。

一个属性可不可枚举是由它的内部属性`[[Enumerable]]`决定的。

获取对象元素的属性描述：

```javascript
Object.getOwnPropertyDescriptor(obj,prop);
```

返回值是一个对象，包含以下属性：

- configurable：是否可配置
- enumerable：是否可枚举
- value：属性值
- writable：是否可写

```javascript
const obj = {a:1,b:2,c:3};
const propDesc = Object.getOwnPropertyDescriptor(obj,'a');
for(const prop in propDesc){
    console.log(prop);
}
console.log(proDesc.enumerable);
```

输出：

```
value
writable
enumerable
configurable
true
```

> 实际上for in遍历的是键名，而不是键值。

因为对象的属性是没有顺序的，所以for in语句以任意顺序遍历对象的属性。

for in语句会遍历对象及其原型链上的所有可枚举属性。

```javascript
const obj = {a:1,b:2,c:3};
const obj2 = Object.create(obj);
obj2.d = 4;
for(const prop in obj2){
    console.log(prop);
}
//输出：d a b c
```

for in语句会遍历数组的索引，而不是数组元素。

```javascript
const arr = [1,2,3];
for(const index in arr){
    console.log(index);
}
//输出：0 1 2
```

# for of

for of语句用于**遍历可迭代对象的元素**。

**可迭代对象**包括Array，Map，Set，String，TypedArray，arguments对象等等。

**元素**指的是数组的值，Map的键值对，Set的值，String的字符等等。

可迭代对象的判断：

```javascript
function isIterable(obj){
    return obj!=null&&typeof obj[Symbol.iterator]==='function';
}
```

```javascript
const arr = [1,2,3];
for(const value of arr){
    console.log(value);
}
//输出：1 2 3

const map = new Map().set('a',1).set('b',2).set('c',3);
for(const value of map){
    console.log(value);
}
//输出：["a", 1] ["b", 2] ["c", 3]

const set = new Set().add(1).add(2).add(3);
for(const value of set){
    console.log(value);
}
//输出：1 2 3

const str = 'abc';
for(const value of str){
    console.log(value);
}
//输出：a b c
```