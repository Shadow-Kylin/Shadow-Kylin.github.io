---
title: 【JavaScript】this
description: 
slug: 
date: 2023-10-10
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
# 简介

this是**函数内部的特殊对象**之一（其他还有`arguments`、`caller`、`new.target`）。

this的指向或值是**不确定的**，**取决于函数的调用方式**。并且在严格模式和非严格模式下的表现也不同。

# 全局上下文中的this

当函数不作为对象的属性被调用时，this指向全局对象，浏览器中是window对象。

```javascript
console.log(this);
this.name = 'Hello';
console.log(window.name); // Hello
```

无论使不使用严格模式，上面的this都指向全局对象。

不论在哪个上下文环境，都可以使用globalThis获取全局对象。

# 函数上下文中的this

函数内部的this由函数的调用方式决定。

```javascript
const obj = {
    getThis: function () {
        console.log(this);
    },
};
function getThis() {
    console.log(this);
}
obj.getThis();
getThis();
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a87dc47bfd144b9085a380ef1612f4c3.png)

若是使用*严格模式*，this的值需要自行指定为任意值，默认为undefined。

```javascript
function getThis() {
    "use strict";
    console.log(this);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/d2eed6af00744db8a13b3217dbeb891c.png)

*函数作为对象方法被调用时，this绑定为调用的对象。*

形式为`obj.func()`，func的this被绑定为obj,无论func是否是obj原型链上的方法。

# 类上下文中的this

类的本质是也是函数。

*类的所有非静态方法都被添加到类的构造函数中的this的原型上。*

```javascript
class Example {
  constructor() {
    const proto = Object.getPrototypeOf(this);
    console.log(Object.getOwnPropertyNames(proto));
  }
  first(){}
  second(){}
  static third(){}
}

new Example(); // ['constructor', 'first', 'second']
```

# 派生类中的this

派生类中的构造函数中的this不会自动绑定，需要使用super()，相当于`this = new Base()`，其中Base为基类。

```javascript
class Base {}
// 派生类没有构造函数
class Good extends Base {}
// 派生类构造函数返回一个对象替换this
class AlsoGood extends Base {
    constructor() {
        return { a: 5 };
    }
}
class Better extends Good {
    constructor() {
        super();
    }
}
// 派生类带有空构造函数
class Bad extends Base {
    constructor() {}
}

new Good();
new AlsoGood();
new Better();
new Bad(); // Uncaught ReferenceError: must call super constructor before using 'this' in derived class constructor
```

# call、apply和bind绑定this

当使用call或apply调用时，this指向传入的第一个参数。

```javascript
func.call(thisArg, arg1, arg2, ...);
func.apply(thisArg, [argsArray]); // argsArray为参数数组
func.bind(thisArg, arg1, arg2, ...);
```

**`call`和`bind`的区别**：*call会立即执行函数，bind不会立即执行函数，而是返回一个新函数，你可以随时调用它。但是bind只生效一次。*

```javascript
function add(c, d) {
    return this.a + this.b + c + d;
}

var o = { a: 1, b: 3 };

// 第一个参数是用作“this”的对象
// 其余参数用作函数的参数
add.call(o, 5, 7); // 16

// 第一个参数是用作“this”的对象
// 第二个参数是一个数组，数组中的两个成员用作函数参数
add.apply(o, [10, 20]); // 34

// 第一个参数是用作“this”的对象
// 其余参数用作函数的参数
let newAdd = add.bind(o, 11, 22);
newAdd(); // 37
newAdd = newAdd.bind({ a: 0, b: 0 }, 11, 22); // 37
```

call、apply和bind如果传入的this参数不是对象，而是原始值：

- 数字，`this = new Number(num)`
- 字符串，`this = new String(str)`
- null或undefined，`this = globalThis`

# 箭头函数中的this

*箭头函数中的this与封闭词法环境的this保持一致。*

通俗点说，就是与定义时而非调用时在的词法环境的this保持一致。

```javascript
var globalObject = this;
var foo = () => this;
console.log(foo() === globalObject); // true

// 作为对象的一个方法调用
var obj = { foo: foo };
console.log(obj.foo() === globalObject); // true

// 尝试使用 call 来设定 this
console.log(foo.call(obj) === globalObject); // true

// 尝试使用 bind 来设定 this
foo = foo.bind(obj);
console.log(foo() === globalObject); // true
```

call、apply和bind都无法改变箭头函数的this。

位于函数中的箭头函数：

```javascript
function foo(){
    return () => this;
}
```

foo返回了一个匿名函数，称为A，那么A返回的this与foo函数的this保持一致，而foo的this与其调用时所在词法环境的this一致。

```javascript
function foo() {
    return () => this;
}
foo()(); // Window
function bar() {
    return foo();
}
bar()(); // Window
const obj = { a:1}
foo.call(obj)().a; // 1
```



# 参考资料

- [this](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)
- [globalThis](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/globalThis)