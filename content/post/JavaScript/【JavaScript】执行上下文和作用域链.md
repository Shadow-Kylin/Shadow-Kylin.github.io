---
title: 【JavaScript】执行上下文与作用域链
description: 
slug: 
date: 2023-08-29
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
# JavaScript 执行上下文

执行上下文决定了**变量和函数可以访问哪些数据**，即代码执行的环境。

一个执行上下文就对应一个仅后台可访问的变量对象，其中保存有该上下文的局部变量、参数和函数声明。

每个执行上下文都有自己的生命周期，当代码执行完成后，执行上下文会被销毁。

最外层的上下文称为**全局上下文**。宿主环境不同，全局上下文的关联对象就不同。在浏览器中，全局上下文就是`window`对象。

> 注意`window`后没有`s`。

除了 window 对象自带的属性外，使用`var`定义的**全局**变量和函数都会成为其属性。

`var`具有**函数作用域**，`let`和`const`具有**块级作用域**。

```javascript
var a1=1;
function func(){
    var a2=2;
}
console.log(a1); //输出1
console.log(typeof a2 === "undefined"); //输出true
console.log(window.a1); //输出1
```

`window.a1`也可以写成`window['a1']`。

一般的上下文在其代码执行完后就销毁，而全局上下文是在程序退出时才被销毁。

执行上下文会按照函数的调用顺序形成一个堆栈，称为**执行上下文栈**。

**当前执行上下文**位于**执行上下文栈**的顶端，当它执行完后会被弹出，程序的控制权就交给之前的上下文。

我们用到的、见到的`this`指针指向的就是这个当前执行上下文，其值取决于函数的调用方式。

# 作用域链

作用域链（scope chain）决定了**变量和函数的访问权限和顺序**，这个链的最前端是当前执行上下文的变量对象。

作用域链的先后顺序就是内外顺序，内部执行上下文包含了外部执行上下文。换句话说，内部执行上下文可以访问外部执行上下文。

我们访问变量和函数的顺序就是从作用域链的前面往后面找。

看个例子：

```javascript
const a4=4;
var a5=5;
const func1 = function (a3) {
    const a2=2;
    function func2() {
        const a1=1;
        return function func3(){
            console.log(a1+a2+a3+a4+a5);
        }
    };
    return func2();
};
const result=func1(3);
result();
```

![image-20230829134446401](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/images/202308291344466.png)

`[[Scopes]]`记录的是变量的父作用域链，可以看到result并没有func3的上下文。

---

## 作用域链增强

有两个影响作用域链的行为：**with**和**try/catch的catch块**。

使用`with`将指定对象的作用域推入上下文栈：

```javascript
let person={
    name:'Carl',
    age:22
}
with(person){
    console.log(name+"'s age is "+age);
}
```

`try/catch`语句的`catch`块是一个单独的上下文，捕获的错误信息被添加到`catch`块的变量对象上，于是只能在`catch`块访问该错误。

```javascript
try {
    // 可能会引发异常的代码
    const result = someFunction();
} catch (error) {
    // 捕获异常并处理
    console.error("An error occurred:", error.name, error.message);
    // 在 catch 块内部可以定义局部变量
    const errorCode = 500;
    console.log("Error code:", errorCode);
}

// 在这里无法访问 error 和 errorCode
console.log(error); // 抛出 ReferenceError
console.log(errorCode); // 抛出 ReferenceError
```

