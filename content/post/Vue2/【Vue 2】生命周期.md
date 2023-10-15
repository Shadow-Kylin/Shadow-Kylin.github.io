---
title: 【Vue 2】生命周期
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
# new Vue()

每个Vue应用都是通过`new Vue()`创建一个Vue实例开始。

Vue()函数可以传入选项Options，常见的有el、template和data选项等。

- el只在new创建实例时生效，其值可以是一个CSS选择器或一个HTML Element实例。实例挂载后（mounted之后）可通过`vm.$el`访问，如果开始实例化时不给el选项，则需要调用`vm.$mount`指定挂载点。挂载元素会被编译后生成的DOM替换掉。
- template定义组件的模板，即要渲染的内容。
- data选项的值是对象类型，它的property会被Vue实例所代理，成为响应式数据，拥有双向改变的特性。只有在创建时data中的属性property才会被响应式化。

Vue()的具体实现如下：

```javascript
import {initMixin} from './init'
function Vue(options){
    //安全性检查：是否为非生产环境且this是否是Vue的实例
    //如果是非生产环境且this不是Vue的实例，即没有通过使用new实例化Vue
    if(process.env.NODE_ENV!=='production'&&!(this instanceof Vue)){
        warn('Vue is a constructor and should be called with the `new` keyword')
    }
    //⭐
    this._init(options)
}
initMixin(Vue)
export default Vue
```

`_init`方法是整个初始化流程的开始。

`initMixin`方法向Vue构造函数的原型prototype挂载一些方法，`_init`便是其中之一。

```javascript
export function initMixin(Vue){
    //在Vue的构造函数的原型上添加一个_init方法
    Vue.prototype._init=function(options){
        //合并构造函数的选项和用户传入的选项并挂载到Vue实例vm的$options选项上
        vm.$options=mergeOptions(
        	resolveConstructorOptions(vm.constructor),
            options||{},
            vm
        )
        //依次初始化
        initLifecycle(vm)	//初始化组件的生命周期
        initEvents(vm)	//初始化事件
        initRender(vm)	//初始化渲染函数
        callHook(vm,'beforeCreate')	//调用beforeCreate生命周期钩子函数
        initInjections(vm)	//初始化注入，处理inject选项
        initState(vm)	//初始化状态，包括data、props、methods、computed、watch
        initProvide(vm)	//初始化provide，处理provide选项
        callHook(vm,'created')	//调用created生命周期钩子函数
        //⭐如果有挂载的目标元素
        if(vm.$options.el){
            //挂载实例到目标元素上
            vm.$mount(vm.$options.el)
        }
    }
}
```

# Vue生命周期图


每个Vue实例在创建时都要经过一系列过程：**初始化**、**模板编译**、**挂载**、**卸载**这四个阶段。阶段之间会陆续执行一类函数，称为**生命周期函数**。如下图所示：

Vue生命周期图

![Vue生命周期图](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151429720.jpeg)

# 生命周期阶段

new Vue()到created的阶段是初始化阶段，该阶段会初始化属性props、事件events和响应式数据data，还有computed、watch、provide和inject。

created到beforeMount的阶段是模板编译阶段，明显Vue的运行时版本不包含该阶段，只存在于完整版Vue中。运行时版本的Vue.js需要搭配其他构建工具如vue-loader等来预编译Vue模板。模板编译即通过编译器将模板代码转换成javascript代码。

beforeMount到mounted阶段是挂载阶段，挂载阶段也是Vue生命周期中占时通常最长的一个阶段。挂载的意思是指Vue.js将其实例挂载到指定的DOM元素上，也就是用编译后的模板代码替换指定DOM元素的内部内容，实现模板内容的渲染。同时，Vue.js会启用Watcher来跟踪依赖的变化。一个状态所绑定的依赖是指一个组件。挂载阶段的原理和`vm.$mount`方法息息相关。

在挂载阶段，数据（状态）发生变化后，Watcher会通知虚拟DOM重新渲染，新DOM将替换旧DOM，从beforeUpdate函数执行后到updated函数结束前是页面重渲染阶段。

最后的卸载阶段，Vue调用`vm.$destroy`方法来销毁自身。

# 生命周期Demo

![image-20230902231204913](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151429486.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.14/dist/vue.js"></script>
    <script>
        Vue.config.productionTip = false;
    </script>
</head>
<body>
    <div id="app">
        <span>点击了{{count}}次</span>
        <button @click="count++">加1</button>
        <button @click="$destroy()">销毁</button>
    </div>
    <script>
        var vm=new Vue({
            el:"#app",
            data:{
                count:0
            },
            beforeCreate(){
                console.log("beforeCreate");
            },
            created() {
                console.log("created");
            },
            beforeMount(){
                console.log("beforeMount");
            },
            mounted(){
                console.log("mounted");
            },
            beforeUpdate() {
                console.log("beforeUpdate");
            },
            updated() {
                console.log("updated");
            },
            beforeDestroy() {
                console.log("beforeDestroy");
            },
            destroyed() {
                console.log("destroyed");
            },
        });
    </script>
</body>
</html>
```

# 面试相关问题

1.最早什么时候可以操作DOM？

最早可以在mounted中操作DOM，因为执行mounted时编译模板已被挂载到DOM元素上。

2.最早什么时候可以使用响应式数据？

由生命周期图上可知，响应式数据初始化在beforeCreate到created之间，所以我们最早可以在created访问响应式数据。

3.第一次页面加载会触发哪些生命周期钩子

beforeCreate、created、beforeMount、mounted

4.父子组件的生命周期执行过程

父组件执行到beforeMount，子组件开始执行生命周期，子组件挂载完成后，父组件开始挂载，父组件挂载完成后，会执行一次更新过程，也就是beforeUpdate、updated。



# 参考资料

- [深入浅出Vue.js](https://book.douban.com/subject/32581281/)
- [vue面试题之一：生命周期函数面试题](https://juejin.cn/post/6844903945530245128)
