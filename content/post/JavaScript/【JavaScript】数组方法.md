---
title: 【JavaScript】数组方法
description: 
slug: 
date: 2023-08-28
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
**JavaScript的数组是动态的，其长度可以随时改变，而且可以存储任意类型的数据**。

# length属性

数组的长度，可以通过该属性改变数组的长度。

用途：`arr.length-1`→arr的最后一个元素被删除。

# 创建数组

常用创建数组的方法：

```javascript
let arr=new Array(19); //创建一个长度为19的数组
let arr=new Array(1,2,3); //创建一个包含1,2,3的数组
let arr=[1,2,3]; //创建一个包含1,2,3的数组
let arr=[]; //创建一个空数组
let arr=[1,2,]; //最后一个逗号会被忽略
let arr=[1,,3]; //中间的空位会被当做undefined
```
ES6新增了两个创建数组的方法：`Array.from()`和`Array.of()`。

## Array.from

`Array.from()`将以下类型的对象转为数组：

- 类数组对象，即可迭代对象。
- 拥有一个`length`属性和可索引元素的对象。

```javascript
//字符串转数组
console.log(Array.from("Hello")); //["H", "e", "l", "l", "o"]

//Map和Set转数组
let map=new Map().set("a",1).set("b",2);
console.log(Array.from(map)); //[["a", 1], ["b", 2]]
let set=new Set().add("a").add("b");
console.log(Array.from(set)); //["a", "b"]

//浅复制数组
let arr=[1,2,3];
console.log(Array.from(arr)); //[1, 2, 3]

//可迭代对象转数组
let iter={
    *[Symbol.iterator](){
        yield 1;
        yield 2;
        yield 3;
    }
}
console.log(Array.from(iter)); //[1, 2, 3]

//arguments对象转数组
function fn(){
    console.log(Array.from(arguments)); 
}
console.log(fn(1,2,3)); //[1, 2, 3]

//带有length属性和可索引元素的对象转数组
let obj={
    length:3,
    0:1,
    1:2,
    2:3
}
console.log(Array.from(obj)); //[1, 2, 3]
```

`Array.from()`第二个参数是一个映射函数，可以直接增强新数组的值，而无需像`Array().from().map()`方法那样先创建一个中间数组。

```javascript
let arr=[1,2,3];
console.log(Array.from(arr,x=>x*2)); //[2, 4, 6]
```

## Array.of

`Array.of`方法可将一参数数组转为数组。

```javascript
function fn() {
    console.log(Array.of(...arguments));
}
fn(1, 2, 3); //[1, 2, 3]
```
Array.of(5)与Array(5)会产生一样的结果吗？

# 检测数组

检测数组的方法：

```javascript
let arr=[1,2,3];
arr instanceof Array //true
Array.isArray(arr) //true
```

使用`instanceof`检测数组的问题在于，**如果数组是在另一个全局执行环境（比如不同的窗口或框架）中创建的，那么`instanceof`就会返回`false`**。

设计一个Demo看看情况：

index.html：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
        let iframe=document.createElement('iframe');
        iframe.src='child-iframe.html';
        iframe.onload=function(){
            let arrInChild=iframe.contentWindow.arrInChild;
            console.log(arrInChild);
            console.log(arrInChild instanceof Array);
            console.log(Array.isArray(arrInChild));
        }
        document.body.appendChild(iframe);
    </script>
</body>
</html>
```


child-iframe.html：


```html
<!DOCTYPE html>
<html>

<head>
    <title>Child IFrame</title>
</head>

<body>
    <script>
        var arrInChild = [1, 2, 3, 4, 5];
    </script>
</body>

</html>
```

在控制台中可以看到：

![](https://gitee.com/Shadow_Kylin/FigureBed/raw/master/img/202310092142385.png)

arrChild是在child-iframe.html中定义的，尽管使用了var声明，instanceof依然无法检测其是否是数组，而使用Array.isArray则生效。

如果使用let声明`arrInChild`，则会报错说`arrInChild`未定义。

# 迭代器方法

ES6新增了三个迭代器方法：`keys()`、`values()`和`entries()`。

- `keys()`：返回一个包含数组中每个索引**键**的迭代器。
- `values()`：返回一个包含数组中每个索引**值**的迭代器。
- `entries()`：返回一个包含数组中每个索引**键值对**的迭代器。

```javascript
let arr=[1,2,3];
console.log(arr.keys()); //[0, 1, 2]
console.log(arr.values()); //[1, 2, 3]
console.log(arr.entries()); //[[0, 1], [1, 2], [2, 3]]

// for...of循环+解构
for(let [index,value] of arr.entries()){
    console.log(index,value);
}
```

# 复制和填充方法

ES6新增了两个复制和填充方法：`copyWithin()`和`fill()`。

## 1.fill()

使用指定值填充数组的指定位置。

fill(value,start,end)：将[start,end)的位置替换为value。

- 如果没有指定start和end，默认填充所有位置。
- 如果只指定start，则end为数组末尾。

```javascript
let arr=[1,2,3];

arr.fill(5);
console.log(arr); //[5, 5, 5]

arr.fill(6,1);
console.log(arr); //[5, 6, 6]

arr.fill(7,1,2);
console.log(arr); //[5, 7, 6]
```

## 2.copyWithin()

从数组内部复制一段内容到数组其他位置，会覆盖原有成员。

copyWithin(target,start,end)：将[start,end)位置的内容复制到target。

```javascript
let arr=[1,2,3,4,5];

//将数组中索引从0开始的元素复制到索引从1开始的位置
arr.copyWithin(1);
console.log(arr); //[1, 1, 2, 3, 4]

//将数组中索引 [1,arr.length) 的元素复制到索引从2开始的位置
arr.copyWithin(2,1);
console.log(arr); //[1, 1, 1, 2, 3]

//将数组中索引 [1,3) 的元素复制到索引从2开始的位置
arr.copyWithin(2,1,3);
console.log(arr); //[1, 1, 1, 1, 3]
```

# 转换方法

1. `toString()`：将数组转换为字符串，每个元素用逗号分隔。对每个元素调用`toString()`方法。

2. `toLocaleString()`：将数组转换为字符串，每个元素用逗号分隔，使用本地特定的分隔符。对每个元素调用`toLocaleString()`方法。

3. `valueOf()`：_返回数组本身_。
4. `join()`：_将数组转换为字符串，每个元素用指定的分隔符分隔_。

```javascript
let arr=[1,2,3];
console.log(arr.toString()); //1,2,3
console.log(arr.toLocaleString()); //1,2,3
console.log(arr.valueOf()); //[1, 2, 3]
console.log(arr.join()); //1,2,3
console.log(arr.join('-')); //1-2-3
```
如果数组中的某个元素是null或undefined，那么该元素在join()、toLocaleString()、toString()和valueOf()方法返回的结果中以空字符串表示。

# 栈方法

ECMAScript数组提供了一些方法，可以让数组像栈一样工作。

- `push()`：将_一个或多个_元素添加到数组的末尾，返回数组的新长度。
- `pop()`：删除数组的最后一个元素，返回该元素。

```javascript
let arr=[1,2,3];
console.log(arr.push(4,5)); //5
console.log(arr); //[1, 2, 3, 4, 5]
console.log(arr.pop()); //5
console.log(arr); //[1, 2, 3, 4]
```

# 队列方法

ECMAScript数组也提供了一些方法，可以让数组像队列一样工作。

- `shift()`：删除数组的第一个元素，返回该元素。

- `unshift()`：将_一个或多个_元素添加到数组的开头，返回数组的新长度。

```javascript
let arr=[1,2,3];
console.log(arr.shift()); //1
console.log(arr); //[2, 3]
console.log(arr.unshift(4,5)); //4
console.log(arr); //[4, 5, 2, 3]
```

# 排序方法

数组有两个排序方法：`reverse()`和`sort()`。

- `reverse()`：反转数组的顺序。

- `sort()`：按升序排列数组项——即最小的值位于最前面，最大的值排在最后面。接受一个比较函数作为参数，用于指定按照什么顺序排列。

```javascript
let arr=[1,2,3];
console.log(arr.reverse()); //[3, 2, 1]
console.log(arr.sort()); //[1, 2, 3]
console.log(arr.sort((a,b)=>b-a)); //[3, 2, 1]
```

# 操作方法

## 1.concat()

先创建当前数组的一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。

```javascript
let arr=[1,2,3];
console.log(arr.concat(4,5)); //[1, 2, 3, 4, 5]
console.log(arr.concat(4,[5,6])); //[1, 2, 3, 4, 5, 6]
console.log(arr.concat([4,[5,6]])); //[1, 2, 3, 4, [5, 6]]
```

`concat()`默认打平一层。

设置`Symbol.isConcatSpreadable`为`false`可以阻止`concat()`打平。

```javascript
let arr = [1, 2, 3];
let arr2 = [4, 5];
arr2[Symbol.isConcatSpreadable] = false;
console.log(arr.concat(arr2)); //[1, 2, 3, [4, 5]]
```

## 2.slice()

基于当前数组中的一个或多个项创建一个新数组。接收两个参数：要返回项的起始位置和结束位置。返回项包括起始位置，但不包括结束位置。

```javascript
let arr=[1,2,3,4,5];
console.log(arr.slice(1)); //[2, 3, 4, 5]
console.log(arr.slice(1,3)); //[2, 3]
console.log(arr.slice(-2)); //[4, 5]
console.log(arr.slice(-2,-1)); //[4]
```

## 3.splice()

**删除、插入和替换数组的元素**，非常强大。接收三个参数：要删除的第一项的位置、要删除的项数和要插入的项。返回值是一个由删除项组成的数组。

```javascript
let arr=[1,2,3,4,5];
console.log(arr.splice(1,2)); //[2, 3]
console.log(arr); //[1, 4, 5]
console.log(arr.splice(1,0,2,3)); //[]
console.log(arr); //[1, 2, 3, 4, 5]
console.log(arr.splice(1,2,6,7)); //[2, 3]
console.log(arr); //[1, 6, 7, 4, 5]
```

# 位置方法

## Ⅰ.按严格相等搜索

严格相等搜索即使用全等符`===`进行比较。

- `indexOf()`：从数组的开头（位置0）开始向后查找，返回要查找的项在数组中的位置；如果没找到，返回-1。

- `lastIndexOf()`：从数组的末尾开始向前查找，返回要查找的项在数组中的位置；如果没找到，返回-1。

- `includes()`：从数组的开头（位置0）开始向后查找，返回一个布尔值，表示是否包含给定的值。

```javascript
let arr=[1,2,3,4,5,4,3,2,1];
console.log(arr.indexOf(3)); //2
console.log(arr.lastIndexOf(3)); //6
console.log(arr.includes(3)); //true
```

## Ⅱ.按回调函数搜索

- `find()`：找到第一个返回值为`true`的项，然后返回该项。如果没找到，返回`undefined`。
- `findIndex()`：找到第一个返回值为`true`的项，然后返回该项的索引。如果没找到，返回-1。

```javascript
let arr=[1,2,3,4,5];
console.log(arr.find((value,index,arr)=>value>3)); //4
console.log(arr.findIndex((value,index,arr)=>value>3)); //3
```

# 迭代方法

- `forEach()`：对数组中的每一项运行给定函数，没有返回值。
- `map()`：对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
- `filter()`：对数组中的每一项运行给定函数，返回该函数会返回`true`的项组成的数组。
- `every()`：对数组中的每一项运行给定函数，如果该函数对每一项都返回`true`，则返回`true`。
- `some()`：对数组中的每一项运行给定函数，如果该函数对任一项返回`true`，则返回`true`。

他们的参数格式一致：**回调函数**、**可选的执行上下文对象**。

回调函数接收三个参数：**数组项的值**、**该项在数组中的位置**和**数组对象本身**。

```javascript
let arr=[1,2,3,4,5];
arr.forEach((value,index,arr)=>console.log(value,index,arr));
//1 0 [1, 2, 3, 4, 5]
//2 1 [1, 2, 3, 4, 5]
//3 2 [1, 2, 3, 4, 5]
//4 3 [1, 2, 3, 4, 5]
//5 4 [1, 2, 3, 4, 5]

console.log(arr.map((value,index,arr)=>value*2)); //[2, 4, 6, 8, 10]

console.log(arr.filter((value,index,arr)=>value>3)); //[4, 5]

console.log(arr.every((value,index,arr)=>value>3)); //false

console.log(arr.some((value,index,arr)=>value>3)); //true
```

forEach回调函数中的this指向是？如何退出forEach?

```java
(function foo() {
    let arr = [1, 2, 3, 4, 5]
    arr.forEach((value, index, arr) => {
        console.log(this) //window
        throw Error('退出forEach')
    })
}())
```

# 归并方法

归并方法：`reduce()`和`reduceRight()`。

- `reduce()`：从数组的第一项开始，逐个遍历到最后。
- `reduceRight()`：从数组的最后一项开始，逐个遍历到第一项。

这两个方法都接收两个参数：**一个在每一项上调用的函数**和（可选的）**作为归并基础的初始值**。

传给`reduce()`和`reduceRight()`的函数接收四个参数：**前一次reduce的返回值**、**当前值**、**当前项的索引**和**数组对象**。这个函数返回的任何值都会作为第一个参数自动传给下一项。

```javascript
let arr=[1,2,3,4,5];
console.log(arr.reduce((prev,cur,index,arr)=>prev+cur)); //15
console.log(arr.reduce((prev,cur,index,arr)=>prev+cur,10)); //25
```