---
title: 【JavaScript】理解对象
description: 
slug: 
date: 2023-10-10
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
# 引言

*浅拷贝、深拷贝*是对*引用类型*而言的。

引用类型的变量对应一个栈区地址，这个栈区地址处存储的值是存放的真正的数据的堆区地址。

基本数据类型的变量也对应一个栈区地址，但是该地址存储的是其真正的值。

`let a = b`发生了什么？

![在这里插入图片描述](https://img-blog.csdnimg.cn/f9ead275dacb461db9574a77ed500201.png)

`let obj2 = obj1`发生了什么？

![在这里插入图片描述](https://img-blog.csdnimg.cn/dc0b299afa3e4ea0af29aa2bf4152d03.png)



JavaScript的数据类型：

![在这里插入图片描述](https://img-blog.csdnimg.cn/52f24fe7c273438493d169438ad1f3af.png)



# 什么是浅拷贝？

浅拷贝（shallow copy）创建的新对象拷贝的是原对象的属性的栈区地址。

![在这里插入图片描述](https://img-blog.csdnimg.cn/8ffe2b7aa09c480aadc3f31e1257bc8b.png)

> 图中同名变量的栈区地址相同，不同名变量的栈区地址不同。

`a`和`_a`、`b`和`_b`都是复制了原来栈区地址的值，对`_a`的修改不会影响`a`，对`_b`的修改却会影响`b`，因为它们相当于`let _b = b`的关系。

# 什么是深拷贝？

深拷贝（deep copy）拷贝对象的堆区数据为新副本，如此新旧对象不会互相影响。

![在这里插入图片描述](https://img-blog.csdnimg.cn/cf26c89064994475896f25138e7531e8.png)

# 浅拷贝的方法有哪些？

1.JavaScript中对象的合并，`Object.assign`本身是浅拷贝。

```javascript
const originalObject = {a:1,b:{c:1}}
const shallowCopy = Object.assign({}, originalObject);
console.log(shallowCopy === originalObject);//false，比较的是栈区地址
shallowCopy.a = 2;
shallowCopy.b.c = 2;
console.log(originalObject.a);// 1
console.log(originalObject.b.c);// 2
```

缺陷：`Object.assign`不会拷贝继承属性、不可枚举属性。

2.展开语法

```javascript
let newObj = {...obj}
```

3.数组的cancat方法

```javascript
const newArr = oldArr.concat([])
```

4.数组的slice方法

```javascript
const newArr = oldArr.slice(start[,end]);
```

5.浅拷贝细致点看，是先创建一个新对象，然后将原对象的属性直接复制到新对象，所以也可以自己写一个浅拷贝函数：

```javascript
function shallowCopy(obj) {
    if (obj === null || typeof obj !== "object") return obj;
    const newObj = new obj.constructor();
    for (let key of Reflect.ownKeys(obj)) {
        newObj[key] = obj[key];
    }
    return newObj;
}
// Object.prototype.d = 1;
const obj1 = { a: 1, b: { c: 1 } };
const obj2 = shallowCopy(obj1);
obj2.a = 2;
obj2.b.c = 2;
```



6.lodash库的浅拷贝方法

# 如何实现深拷贝？

1.`JSON.stringify()`与`JSON.parse()`

```javascript
function deepClone(obj){
    if(obj === null || typeof obj !== 'object') return obj;
    return JSON.parse(JSON.stringify(obj));
}
```

缺陷：

- 丢失function、undefined、Symbol这几种类型的键值对
- NaN、Infinity的值会转为null
- Date会变为字符串
- RegExp会变成空对象
- 不能拷贝不可枚举属性及原型链上的属性
- 不能解决循环引用

2.lodash库的深拷贝方法

3.手动实现深拷贝函数基础版：

```javascript
function deepClone(obj) {
    if(obj === null || typeof obj !== 'object') return obj;
    const newObj = new obj.constructor();
    for (let key of Reflect.ownKeys(obj)) {
        newObj[key] = typeof obj[key] === "object" && obj[key] !== null ? arguments.callee(obj[key]) : obj[key];
    }
    return newObj;
}
```

> `const newObj = new obj.constructor()`相比于使用`{}`，保持了原型链的继承。

缺陷：

- 不能处理*循环引用*，可能会导致堆栈溢出
- 对`Array`、`Date`、`RegExp`、`Map`、`Set`对象的处理不好

4.手动实现深拷贝函数进阶版：

```javascript
const deepClone = function (obj, hash = new WeakMap()) {
    if(obj === null || typeof obj !== 'object') return obj;
    
    // 防止循环引用
    if (hash.has(obj)) return hash.get(obj);

    // 如果参数为Date, RegExp, Set, Map, WeakMap, WeakSet等引用类型，则直接生成一个新的实例
    let type = [Date, RegExp, Set, Map, WeakMap, WeakSet];
    if (type.includes(obj.constructor)) return new obj.constructor(obj);

    const newObj = new obj.constructor();
    
    for (let key of Reflect.ownKeys(obj)) {
        newObj[key] = typeof obj[key] === "object" && obj[key] !== null ? arguments.callee(obj[key]) : obj[key];
    }

    // 哈希表设值
    hash.set(obj, cloneObj);

    return cloneObj;
};
```



# 参考资料

- [JavaScript中浅拷贝和深拷贝的区别与实现](https://blog.csdn.net/icemwj/article/details/119782133#:~:text=JavaScript%E4%B8%AD%E6%B5%85%E6%8B%B7%E8%B4%9D%E5%92%8C%E6%B7%B1%E6%8B%B7%E8%B4%9D%E7%9A%84%E5%8C%BA%E5%88%AB%E4%B8%8E%E5%AE%9E%E7%8E%B0%201%201%E3%80%81%E6%B7%B1%E6%8B%B7%E8%B4%9D%E5%92%8C%E6%B5%85%E6%8B%B7%E8%B4%9D%E7%9A%84%E5%8C%BA%E5%88%AB%20%E6%B5%85%E6%8B%B7%E8%B4%9D%EF%BC%88shallow%20copy%EF%BC%89%EF%BC%9A%E5%8F%AA%E5%A4%8D%E5%88%B6%E6%8C%87%E5%90%91%E6%9F%90%E4%B8%AA%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%8C%87%E9%92%88%EF%BC%8C%E8%80%8C%E4%B8%8D%E5%A4%8D%E5%88%B6%E5%AF%B9%E8%B1%A1%E6%9C%AC%E8%BA%AB%EF%BC%8C%E6%96%B0%E6%97%A7%E5%AF%B9%E8%B1%A1%E5%85%B1%E4%BA%AB%E4%B8%80%E5%9D%97%E5%86%85%E5%AD%98%E3%80%82%20%E6%B7%B1%E6%8B%B7%E8%B4%9D%EF%BC%88deep%20copy%EF%BC%89%EF%BC%9A%E5%A4%8D%E5%88%B6%E5%B9%B6%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E4%B8%80%E6%A8%A1%E4%B8%80%E6%A0%B7%E7%9A%84%E5%AF%B9%E8%B1%A1%EF%BC%8C%E4%B8%8D%E5%85%B1%E4%BA%AB%E5%86%85%E5%AD%98%EF%BC%8C%E4%BF%AE%E6%94%B9%E6%96%B0%E5%AF%B9%E8%B1%A1%EF%BC%8C%E6%97%A7%E5%AF%B9%E8%B1%A1%E4%BF%9D%E6%8C%81%E4%B8%8D%E5%8F%98%E3%80%82%202,3.1%20%E9%80%92%E5%BD%92%E9%81%8D%E5%8E%86%E6%89%80%E6%9C%89%E5%B1%82%E7%BA%A7%EF%BC%8C%E5%AE%9E%E7%8E%B0%E6%B7%B1%E6%8B%B7%E8%B4%9D%20%E6%88%91%E4%BB%AC%E9%87%87%E7%94%A8%20%E9%80%92%E5%BD%92%20%E7%9A%84%E6%96%B9%E5%BC%8F%E6%9D%A5%E5%B0%81%E8%A3%85%E5%AE%9E%E7%8E%B0%E6%B7%B1%E6%8B%B7%E8%B4%9D%E7%9A%84%E5%87%BD%E6%95%B0%E3%80%82%20%2F%2F%201.%20)
- [JavaScript深拷贝和浅拷贝看这篇就够了](https://juejin.cn/post/6994453856063062053)
- [关于堆栈的讲解(我见过的最经典的)](https://blog.csdn.net/yingms/article/details/53188974)
- [深入理解js数据类型与堆栈内存](https://juejin.cn/post/6942880039897825294#heading-15)
- [js中深浅拷贝的实现方式(含图解原理)](https://blog.csdn.net/weixin_42467467/article/details/106147205)
- [JavaScript深拷贝看这篇就行了！（实现完美的ES6+版本）](https://blog.csdn.net/cc18868876837/article/details/114918262)
- [【JavaScript】arguments.callee的作用及替换方案](https://blog.csdn.net/u013451157/article/details/78686881)
- [[javascript核心-15]手写完美深拷贝代码实现🍌](https://juejin.cn/post/7247278895338913851)
- [JS中JSON序列化JSON.stringify的坑点和处理](https://juejin.cn/post/7077487024474685470)
- [Reflect.ownKeys()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys)