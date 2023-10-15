---
title: 【Vue 2】Object的依赖追踪原理
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
# 1 前言

学习Vue的过程中，我们常常会注意到<span style="color:red">依赖追踪</span>这个词，什么是依赖？又该如何追踪呢？

首先，我们知道Vue的响应式系统是其独特的特性，经典的一句话：视图随着状态变化而变化。而依赖追踪与其工作原理联系紧密。

从状态生成DOM，再显示到用户界面的过程称为渲染。响应式系统则负责页面的重渲染。所以，大致重渲染过程应该是**状态变化->重渲染**。要及时侦测到数据的变化，就要依靠Vue的变化侦测系统，就是我们所说的依赖追踪。

这篇文章，我们仅讨论Object的变化侦测。关于Array的变化侦测部分我们在另一篇文章再谈。

变化侦测，侦测的是该变化隶属于哪些DOM元素。

在Vue 2.0之前，一个依状态会绑定在多个依赖，一个依赖对应一个DOM节点，所以当该状态改变时就通知其所有依赖进行更新。

Vue 2.0引入了**虚拟DOM**的概念，虚拟DOM使得一个依赖不再是具体的DOM节点，而是一个组件，如此，状态变化时，组件内部将虚拟DOM与真实DOM比对。因此，我们不再需要对每个状态绑定的DOM元素进行追踪，减少了大量依赖，降低了追踪依赖所消耗的内存负担。

# 2 具体实现

接下来，就谈谈变化侦测具体的JavaScript实现。

JasvaScript中，`Object.defineProperty`和ES6的`Proxy`都可以侦测数据的变化。**Vue 2使用了Object.defineProperty，Vue3则使用了更为强大的Proxy**。

本文仅讨论Vue2。

## 2.1 defineReactive函数

一个简单的响应式系统如下：

```javascript
// data为属性对象，key为属性名，val为属性初始值
function defineReactive(data,key,val){
    //dep：依赖收集数组，用于存储属性的订阅者，也就是观察者
    //当属性被访问时，订阅者被添加到dep，以便在属性更新时进行通知
    let dep=[];
    Object.defineProperty(data,key,{
        //表示该属性可被枚举
        enumerable:true,
        //表示该属性可被修改或删除
        configurable:true,
        //getter方法，属性被访问时，执行该函数
        get:function(){
            //将当前订阅者添加到dep数组
            dep.push(window.target)
            //返回当前值
            return val
        },
        //setter函数，属性被修改时，执行该函数
        set:function(newVal){
            //新值与旧值相等就不更新
            if(val===newVal){
                return
            }
            //否则遍历dep数组，更新所有依赖的值
            for(let i=0;i<dep.length;i++){
                dep[i](newVal,val)
            }
            //更新值
            val=newVal
    	}
    })
}
```

上述代码定义了一个 `defineReactive` 函数，它将属性对象 `data` 中的属性 `key` 转化为响应式属性。每个响应式属性都与一个名为 `dep` 的依赖管理器相关联。当属性被访问时，订阅者（观察者）会被添加到 `dep` 数组中，以便在属性更新时通知它们。

## 2.2 Dep类

我们可以将依赖收集的代码封装成一个Dep类，降低代码耦合度：

```javascript
export default class Dep{
    constructor(){
        this.subs=[]
    }
    //添加一个订阅者（观察者）到依赖列表
    addSub(sub){
        this.subs.push(sub)
    }
    //从依赖列表中移除一个订阅者
    removeSub(sub){
        remove(this.subs,sub)
    }
    //在属性被访问时，将当前订阅者添加到依赖列表中
    depend(){
        if(window.target){
            this.addSub(window.target)
        }
    }
    //在属性更新时，通知所有订阅者进行更新操作
    notify(){
        const subs=this.subs.slice();
        for(let i=0;i<subs.length;i++){
            subs[i].update();
        }
    }
}
function remove(arr,item){
    if(arr.length){
        const index=arr.indexOf(item)
        if(index>-1){
            return arr.splice(index,1)
        }
    }
}
```

再修改defineReactive：

```javascript
function defineReactive(data,key,val){
    let dep=new Dep()
    Object.defineProperty(data,key,{
        enumerable:true,
        configurable:true,
        get:function(){
            dep.depend()
            return val
        },
        set:function(newVal){
            if(val===newVal){
                return
            }
        	val=newVal
            dep.notify()
    	}
    })
}
```

## 2.3 Watcher类

上面代码中，我们收集的是`window.target`，实际上它既可以是用户模板中的表达式，也可以是一个`watch`、`computed`，于是Vue抽象出一个Watcher类来管理这多种情况，我们只需收集Watcher实例即可。

Watcher是对依赖关系的封装，它的主要作用是建立依赖关系，当数据变化时通知相关的 `Watcher` 执行更新操作。

因此，依赖变成了**数据属性与Watcher的关系**。**一个依赖列表里是同一个属性的依赖，包含了依赖于该属性的Watcher**。

使用`vm.$watch`方法建立Watcher：

```javascript
//keypath
//a.b.c是属性路径，位于data属性
vm.$watch('a.b.c',function(newVal,oldVal){
    //...
})
```

下面是简化版的Watcher实现：

```javascript
export default class Watcher {
    // vm：Vue 实例，expOrFn：表达式或函数，callback：回调函数
    constructor(vm, expOrFn, callback) {
        this.vm = vm;
        // 属性获取函数
        this.getter = parsePath(expOrFn);
        this.callback = callback;
        this.value = this.get();
    }
    // 获取属性值
    get() {
        // 将当前的 Watcher 实例设置为全局变量
        window.target = this;
        // 通过 getter 获取 Vue 实例的属性值，同时触发依赖收集
        let value = this.getter.call(this.vm, this.vm);
        // 清除全局变量
        window.target = undefined;
        return value;
    }
    update() {
        // 获取旧值
        const oldValue = this.value;
        // 重新获取值
        this.value = this.get();
        // 调用回调函数，传递新旧值作为参数
        this.callback.call(this.vm, this.value, oldValue);
    }
}
```

我们通过Watcher读取数据时，会触发getter方法将Watcher实例添加到依赖中。当数据发生了变化，会触发setter，从而向依赖管理器Dep中的依赖（Watcher）列表发送更新通知。

下面是parsePath的简单代码实现：

```javascript
//检查路径字符串是否包含除了字母、数字、下划线、点及美元符号之外的特殊字符
const bailRE=/[^\w.$]/
export function parsePath(path){
    if(bailRE.test(path)){
        return
    }
    const segments=path.splits('.')
    return function(obj){
        for(let i=0;i<segments.length;i++){
            if(!obj) return
            obj=obj[segments[i]]
        }
        return obj
    }
}
```

`parsePath` 函数用于解析属性路径字符串，确保它仅包含字母、数字、下划线、点和美元符号。它返回一个函数，该函数接受一个对象作为参数，可以根据属性路径访问对象的属性。

## 2.4 Observer类

前面的代码只能侦测一个属性，且不包括其子属性。为了能够侦测 `data` 中所有属性及其子属性的变化，需要使用 `Observer` 类来遍历并将它们都转化为 getter/setter 的形式：

```javascript
export class Observer {
    constructor(value) {
        this.value = value;
        // 非数组
        if (!Array.isArray(value)) {
            this.walk(value);
        }
    }
    walk(obj) {
        const keys = Object.keys(obj);
        for (let i = 0; i < keys.length; i++) {
            // 对每个属性调用defineReactive
            defineReactive(obj, keys[i], obj[keys[i]]);
        }
    }
}
```
```javascript
// 定义响应式数据
function defineReactive(data, key, val) {
    // 是对象类型，就使用Observer遍历其属性
    if (typeof val === "object") {
        new Observer(val);
    }
    // 依赖管理器
    let dep = new Dep();
    Object.defineProperty(data, key, {
        enumerable: true,
        configurable: true,
        get: function () {
            // 依赖收集，即添加Watcher
            dep.depend();
            return val;
        },
        set: function (newVal) {
            if (val === newVal) {
                return;
            }
            val = newVal;
            // 通知依赖更新
            dep.notify();
        },
    });
}
```

当Vue实例化时，Observer会遍历数据对象的属性。

依赖追踪的过程基本上就这样，但要注意有一个缺陷：**无法追踪新增属性和删除属性，只能追踪属性是否被修改**。于是Vue.js提供了`vm.$set`和`vm.$delete`来解决该问题。

# 3 总结

数据属性（Data Properties）的依赖追踪是 Vue 响应式系统的核心之一，它使得数据与视图能够自动关联，从而实现了视图随着数据变化而变化的特性。在 Vue 中，依赖追踪主要包括以下关键点：

- `Dep` 类：用于管理依赖（观察者），包括添加、移除依赖和通知依赖更新的功能。
- `Watcher` 类：用于建立依赖关系，当数据变化时通知相关的 `Watcher` 执行更新操作。
- `defineReactive` 函数：将数据属性转化为响应式属性，并管理与之相关的依赖。
- `Observer` 类：遍历对象的属性，将它们都转化为 getter/setter 的形式，以便进行依赖追踪。

整个依赖追踪的过程可以概括为：

1. 数据属性被观察者遍历，每个属性都被转化为响应式属性。
2. 每个响应式属性与一个依赖管理器关联，用于管理依赖的订阅者。
3. 当数据属性被访问时，订阅者（观察者）被添加到依赖列表中，以便在属性更新时通知它们。
4. 当数据属性发生变化时，依赖管理器会通知所有订阅者执行更新操作，确保视图与数据保持同步。
