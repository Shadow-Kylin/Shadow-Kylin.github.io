---
title: 【Vue 2】处理边界情况
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---

# 访问元素和组件

通过[Vue 2 组件基础](https://blog.csdn.net/2201_75288929/article/details/132277897?ops_request_misc=&request_id=7e17991ac25a490c92197e690ca26f04&biz_id=&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~koosearch~default-3-132277897-null-null.268^v1^control&utm_term=vue%202&spm=1018.2226.3001.4450)一文的学习，我们知道组件之间可以通过传递**props**或**事件**来进行通信。

但在一些情况下，我们使用下面的方法将更有用。

---

**1.访问根实例**

根实例可通过`this.$root`获取。

我们在所有子组件中都可以像上面那样访问根实例，它就像一个`vuex`中的全局`store`。

这样我们可以在根实例上定义方法和属性，这些方法和属性可以在所有组件中访问。

根实例也可以用作事件总线，你可以触发根实例上的自定义事件，在其他组件上监听这些事件以进行通信。一个示例如下：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue 事件总线</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <button @click="sendMessage">Send Message By Root</button>
        <child-component></child-component>
    </div>
    <script>
        //  注册组件
        Vue.component('child-component', {
            data: function () {
                return {
                    childMessage:'childMsg'
                }
            },
            template: 
            `
            <div>
                <p>Message in ChildComponent：{{childMessage}}</p>
            </div>
            `,
            created(){
                //通过 this.$root 监听`message-sent`事件
                this.$root.$on('message-sent',message=>{
                    this.childMessage=message
                })
            }
        });
        var vm = new Vue({
            el: '#app',
            data:{
                message:'rootMsg'
            },
            methods:{
                sendMessage(){
                    //在根实例上触发自定义事件'message-sent'
                    this.$emit('message-sent',this.message)
                }
            }
        });
    </script>
</body>

</html>
<script>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151425851.gif)

---

**2.访问父组件实例、访问子组件实例或子元素**

之前我们可以通过传递`prop`来达到 *子组件访问父组件实例* 或者称为 *父组件向子组件传值* 的目的。

现在我们可以使用`$parent` property来访问父级组件实例。

之前我们也通过触发自定义事件传递`抛出值`的方式来访问子组件实例。

同样的，我们可以通过`ref` attribute为子组件或子元素添加一个 ID 引用。

```html
<child-component ref="child"></child-component>
```

于是可以使用`this.$refs.child`来访问子组件实例。

**通过ref引用子组件**示例：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue ref 示例</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <button @click="toggleColor">Toggle Color</button>
        <p :style="{ color: textColor }">This text color can be toggled.</p>
        <child-component ref="childRef"></child-component>
    </div>
    <script>
        // 子组件
        Vue.component('child-component', {
            template:
                `
            <div>
                <p>This is a child component.</p>
            </div>
            `,
            methods: {
                sayHello() {
                    console.log('Hello from child component!');
                }
            }
        });

        var vm = new Vue({
            el: '#app',
            data: {
                textColor: 'black',
                isChildVisible: true
            },
            methods: {
                toggleColor() {
                    this.textColor = this.textColor === 'black' ? 'red' : 'black';
                    // 通过 ref 访问子组件实例中的方法
                    this.$refs.childRef.sayHello();
                }
            }
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151425932.gif)

---

**3.依赖注入**

不管是`this.$parent`、`this.$refs`，都可能会陷入深层嵌套的风险，我们可能会通过`this.$parent.$parent.$parent`来访问上层某个组件实例。这无疑是不合理的。

针对较深层级的组件访问，Vue设计了依赖注入的模式。它允许我们将一个依赖注入到某个组件里，以便组件可以访问这个依赖而不需要显示传递它。这对于共享全局配置或服务非常有用，例如国际化（i18n）配置、数据请求服务等。

具体的，我们通过在父组件`provide`选项提供依赖，然后在子组件的`inject`选项注入依赖。

示例：

```javascript
//在父组件中
provide:{
	userSevice:new UserService()
}

//在子组件中
inject:['userService'],
created(){
    this.userService.doSomething()
}
```

为什么我们不使用`$root`实现这种方案呢？我认为，<mark>依赖注入适合小范围的配置共享，而全局共享则适用于`$root`</mark>。

# 程序化的事件监听器

`$emit`触发的事件可以被`v-on`监听，但这不是我们这章的内容。

本文主要介绍 Vue 2 中程序化的事件监听器，我们可以使用它来动态地添加和删除事件监听器。

Vue实例上有一系列方法来处理事件，包括`$on`、`$once`和`$off`。它们都包含两个参数：`eventName`和`eventHandler`。`$on`侦听一个事件；`$once`只侦听一个事件一次；`$off`停止侦听一个事件。

`$emit`、`$on`和`$off`与浏览器的EventTarget API 中的`dispatchEvent`、`addEventListener`和`removeEventListener`并不等同。

`$emit`用于触发自定义事件，`dispatchEvent`用于触发各种DOM事件。`addEventListener`和`removeEventListener`同理。

# 组件的循环引用

**1.递归组件**

我们可以在组件模板内部调用自身，这称为**递归组件**。递归组件要注意设置递归终止条件，例如v-if的值为false。

假设我们有一个表示文件夹结构的数据对象：

```javascript
<!DOCTYPE html>
<html>

<head>
    <title>Vue 2 递归组件 Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <recursive-folder :folder="folder"></recursive-folder>
    </div>
    <script>
        Vue.component('recursive-folder', {
            props: ['folder'],
            template: `
                <div>
                    <span>{{ folder.name }}</span>
                    <div v-if="folder.children.length > 0">
                        <ul>
                            <li v-for="childFolder in folder.children" :key="childFolder.name">
                                <recursive-folder :folder="childFolder"></recursive-folder>
                            </li>
                        </ul>
                    </div>
                </div>
            `
        });
        var vm = new Vue({
            el: '#app',
            name: 'recursive-folder',
            data: {
                folder: {
                    name: 'Folder A',
                    children: [
                        {
                            name: 'Subfolder A.1',
                            children: [
                                {
                                    name: 'File A.1.1',
                                    children: []
                                },
                                // 可能有更多子项
                            ]
                        },
                        {
                            name: 'File A.2',
                            children: []
                        },
                        // 可能有更多子项
                    ]
                }
            },
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151426293.png)

---

**2.循环组件**

<u>循环组件是不同组件互相使用，递归组件是自身使用自身</u>。

使用`Vue.component`全局注册组件时，Vue帮助我们自动解决组件之间的依赖关系，因此你可以随意使用它们。

但是，当我们使用**模块系统**（如Webpack）时，便可能出现循环依赖的情况。

为了解决该问题，你可以在组件的`beforeCreate`生命周期钩子函数中动态注册依赖组件，或者使用异步组件加载来确保组件能够正确解析和注册：

**1）在组件的`beforeCreate`生命周期钩子函数中动态注册依赖组件**：

```javascript
beforeCreate(){
    this.$options.components.AnotherComponent=require("./another-component.vue").default
}
```

**2）使用异步组件加载**：

参考[Vue 2 动态组件和异步组件](https://blog.csdn.net/2201_75288929/article/details/132366964?spm=1001.2014.3001.5501)一文。

---

# 模板定义的新方式

通常，我们使用组件内的`template`选项定义模板字符串，或者在`.vue`文件的`<template>`元素中定义模板。

**1.内联模板**

如果在子组件上添加`inline-template` attribute，那么子组件将使用其内部内容作为模板，而不会将其作为[插槽内容](https://blog.csdn.net/2201_75288929/article/details/132277897?csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%22132277897%22%2C%22source%22%3A%222201_75288929%22%7D#简单了解通过插槽分发内容)。

```html
<child-component inline-template>
	<div>
        <!--模板内容-->
    </div>
</child-component>
```

---

**2.X-Template**

```html
<script type="text/x-template" id="my-template">
	<div>
		<!--模板内容-->
	</div>
</script>
```

```javascript
Vue.component('my-component',{
    template:'#my-template'
})
```

这两种方式适用于我们做做Demo或很小的项目。

# 控制更新

控制更新指**强制更新**和**阻止更新**。

---

**1.强制更新**

使用`$forceUpdate`方法强制更新一个组件，即使没有任何状态变化也会触发重新渲染。

不过，只有调用它的组件实例及该实例中的子组件的插槽内容会被强制更新，子组件本身不受影响。

通过递归算法可以强制更新整个组件树：

```javascript
function foreceUpdateRecursive(vm){
    vm.$forceUpdate()
    vm.$children.forEach(child=>{
        foreceUpdateRecursive(child)
    })
}
```

---

**2.阻止更新：使用v-once创建低开销的静态组件**

静态意味着数据不再改变，也就不用重新渲染。

`v-once`保证了只渲染该元素和其子元素一次，且将它们的数据缓存起来。

