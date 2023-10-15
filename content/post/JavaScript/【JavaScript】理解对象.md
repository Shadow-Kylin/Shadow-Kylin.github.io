---
title: 【JavaScript】理解对象
description: 
slug: 
date: 2023-09-16
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
**ECMA-262将对象定义为一组属性的无序集合**。

# 1 内部属性描述

## 1.1 数据属性

- `[[Configurable]]`：可配置性，直接定义在对象的属性该特性默认为 true，表示可以对属性进行删除、修改等操作。
- `[[Enumerable]]`：可枚举性，直接定义在对象的属性该特性默认为 true，它决定了我们是否可以通过`for-in`循环遍历对象的属性。
- `[[Writable]]`：可写性，直接定义在对象的属性该特性默认为 true，表示属性的值是否可以被修改。
- `[[Value]]`：属性的实际数据值，默认为 undefined。

我们可以使用`Object.defineProperty`来定义数据属性。

```javascript
let student1 = { name: "student1" };
Object.defineProperty(student1, "sex", {
    configurable: false,
    enumerable: false,
    writable: false,
    value: "male",
});
//测试可配置性
delete student1.sex;
console.log(student1.sex);
console.log();
//测试可枚举性
for (const key in student1) {
    console.log(key + " ");
}
console.log();
//测试可写性
student1.sex = "female";
console.log(student1.sex);
```

输出：

```
male

name

male
```

除了属性`configurable`属性为`false`的情况，我们可以多次调用`defineProperty`。

如果调用了 `defineProperty` 但不指定 `configurable`、`enumerable`、`writable`的值，则它们默认被设为 `false`。

## 1.2 访问器属性

- `[[Configurable]]`：表示属性是否可修改。直接定义在对象的属性该特性默认为true。
- `[[Enumberable]]`：表示属性是否可枚举。直接定义在对象的属性该特性默认为true。
- `[[Get]]`：获取属性时调用的函数。默认为undefined。
- `[[Set]]`：设置属性时调用的函数。默认为undefined。

同样我们使用`Object.defineProperty`来定义访问器属性。

```javascript
let person = {
    firstName: "John",
    lastName: "Doe",
};
Object.defineProperty(person, 'fullName', {
    get() {
        return this.firstName + " " + this.lastName;
    },
    set(value) {
        const parts = value.split(" ");
        this.firstName = parts[0];
        this.lastName = parts[1];
    }
});
console.log(person.fullName);
person.fullName = "Jane Smith";
console.log(person.firstName);
console.log(person.lastName);
```

输出：

```
John Doe
Jane
Smith
```

访问器属性和数据属性的不同之处在于：**数据属性关注于数据的值与可写性，访问器属性关注获取和设置数据时进行的操作**。

# 2 定义多个属性

可以使用`Object.defineProperties`一次性定义多个属性。

```javascript
let person={};
Object.defineProperties(person,{
    name:{
        value: 'Jane'
    },
    age:{
        get(){
            return this.age;
        },
        set(value){
            this.age=value;
        }
    }
});
```

# 3 读取属性特性

使用`Object.getOwnPropertyDescriptor`读取属性的属性描述符。

使用上面一节中定义好的person对象。

```javascript
let descriptor1=Object.getOwnPropertyDescriptor(person,'name');
console.log('descriptor1.configurable：'+descriptor1.configurable);
console.log('descriptor1.enumerable：'+descriptor1.enumerable);
console.log('descriptor1.writable：'+descriptor1.writable);
console.log('descriptor1.value：'+descriptor1.value);
let descriptor2=Object.getOwnPropertyDescriptor(person,'age');
console.log('descriptor2.configurable：'+descriptor2.configurable);
console.log('descriptor2.enumerable：'+descriptor2.enumerable);
console.log('descriptor2.get：'+descriptor2.get);
console.log('descriptor2.set：'+descriptor2.set);
```

运行结果：

```
descriptor1.configurable：false
descriptor1.enumerable：false
descriptor1.writable：false
descriptor1.value：Jane
descriptor2.configurable：false
descriptor2.enumerable：false
descriptor2.get：get() {
            return this.age;
        }
descriptor2.set：set(value) {
            this.age = value;
        }
```

注意到没有设置的configurable、enumerable、writable都被设置为false。

# 4 合并对象

合并对象即将一个对象的属性复制到目标对象上，这也被称为**混入**（mixin）。学过Vue的知道，Vue里面也有个混入的概念，[Vue2 混入](https://blog.csdn.net/2201_75288929/article/details/132385363?ops_request_misc=&request_id=eeda0e23ba4f4167a57ea6cd2d5c86e3&biz_id=&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~koosearch~default-1-132385363-null-null.268^v1^control&utm_term=Vue%E6%B7%B7%E5%85%A5&spm=1018.2226.3001.4450)。

ES6提供Object.assign()方法来合并对象。

该方法将一个或多个源对象的**可枚举和自有属性**复制到目标对象上。

- 可枚举判断：**Object.propertyIsEnumerable()**。
- 自有属性判断：**Object.hasOwnProperty()**。

复制的具体操作是调用源对象的get获取属性，再调用目标对象的set属性。

```javascript
const target = {
    _a:'',
    // set a(val) {
    //     console.log("调用target属性a的set");
    //     this._a = val;
    // }
};
Object.defineProperty(target, 'a', {
    set(val) {
        console.log('调用target属性a的set');
        this._a = val;
    },
});
const source1 = {
    // get a() {
    //     console.log("调用source1属性a的get");
    //     return "a";
    // }
};
Object.defineProperty(source1, "a", {
    get() {
        console.log("调用source1属性a的get");
        return "a";
    },
    enumerable: true
});
const source2 = { b: 'b' };
Object.assign(target, source1, source2);
console.log(target);
```

在对象字面量中定义的属性的未设置的数据属性和访问器属性都为默认值，而在`Object.defineProperty`中定义的属性的未设置的特性要么为`false`，要么为`undefined`。

上面我们给`source1`的访问器属性`a`设置`enumerable: true`就是这个原因，不然无法复制`a`属性。

运行结果：

```
调用source1属性a的get
调用target属性a的set
{ _a: 'a', b: 'b' }
```

`Object.assign`还有以下注意的地方：

1. **遇到同名的属性，后面的会覆盖前面的**。

2. **Object.assign是浅复制，即只复制对象内部元素的引用**。

   ```javascript
   const originalObject={a:1}
   const anotherShallowCopy = Object.assign({}, originalObject);
   console.log(anotherShallowCopy === originalObject);//false
   console.log(anotherShallowCopy.a === originalObject.a);//true
   ```

3. **在复制过程出现错误，Object.assign不会回滚到初始状态**。

# 5 严格相等判断

大多数情况我们可以使用`===`来判断严格相等，但以下的特殊边界情况却不尽人意：

```javascript
console.log(+0 === -0); //true
console.log(+0 === 0); //true
console.log(-0 === 0); //true
console.log(NaN === NaN); //false
```

其中**NaN**表示**Not a Number**，可以使用全局函数**isNaN**检查。

ES6提供了`Object.is`来应对上述所有情况。

```javascript
Object.is(v1,v2);
```

使用递归+Object.is+剩余参数比较多个值的相等性：

```javascript
function areValuesEqual(a,...values){
    return Object.is(a,values[0])&&(values.length<2||areValuesEqual(...values));
}
```

或者使用`every`：

```javascript
function areValuesEqual(a, ...values) {
  return values.every(value => Object.is(a, value));
}
```

# 6 增强的对象语法

## 6.1 属性值的简写

```javascript
const name = "John";
const age = 30;

// 使用属性值简写创建对象
const person = {
  name,
  age,
  sayHello() {
    console.log(`Hello, my name is ${this.name}.`);
  }
};

console.log(person); // 输出 { name: 'John', age: 30 }
person.sayHello(); // 输出 "Hello, my name is John."
```

## 6.2 可计算属性

可计算属性允许你在定义对象时在方括号内使用表达式来定义对象名。

```javascript
const methodSuffix = "Hello";

const dynamicObject = {
  ["say" + methodSuffix]() {
    console.log("Hello, World!");
  }
};

dynamicObject.sayHello(); // 输出 "Hello, World!"
```

## 6.3 简写属性名

```javascript
let key='Age';
let person={
    _name: 'Jane',
    _age: '18',
    get name(){
        return this._name;
    },
    set name(val){
        this._name=val;
    },
	sayHello() {
    	console.log(`Hello, my name is ${this._name}.`);
  	},
    ['say'+key](){
        console.log(`Hello, my age is ${this._age}.`);
    }
};
```

# 7 对象解构

对象解构指可以使用与对象匹配的结构来实现对象属性赋值。

```javascript
let person={
    name: 'Jane',
    age: 18
};
//别名
let {name:personName,age:personAge}=person;
//属性简写
let {name,age}=person;
//没有匹配的属性被赋值为undefined
let {name,age,height}=person;
//解构时赋值
let {name,age,height=180}=person;
```

解构的本质是使用**ToObject**函数将元数据转为对象。

因此，null和undefined不能被解构。

<u>如果给事先声明好的变量赋值，则解构表达式整个需要包裹在一对括号里</u>。

**1）嵌套解构**

```javascript
let {outer:{inter}}=obj;
```

**2）部分解构**

解构像我们的对象合并一样，是尽力而为的行为，一旦解构过程发生错误，过程终止且无法回滚，也就是只解构了部分。

**3）对象解构为函数参数**

```javascript
function sum([a, b]) {
  return a + b;
}

const numbers = [3, 5];

console.log(sum(numbers)); // 输出 8

```

