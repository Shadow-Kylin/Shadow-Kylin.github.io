---
title: 【Vue 2】动态组件和异步组件
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
先阅读 [【Vue 2 组件基础】中的初步了解动态组件](https://blog.csdn.net/2201_75288929/article/details/132277897?csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%22132277897%22%2C%22source%22%3A%222201_75288929%22%7D#初步了解动态组件)。

# 动态组件

我们知道动态组件使用`is`属性和`component`标签结合来切换不同组件。

下面给出一个示例：

```html
<div id="app">
    <!--  tab 标签页 -->
    <div>
        <button @click="tab = 'home'">首页</button>
        <button @click="tab = 'posts'">文章</button>
    </div>
    父组件count: {{count}}
    <!--  动态组件 -->
    <component :is="tab" @increment="count=$event"></component>
</div>
<script>
    //  注册组件
    Vue.component('home', {
        data: function () {
            return {
                count: 0
            }
        },
        template: 
        `
        <div>
            <div>首页内容</div>
            <div>子组件count: {{count}}</div>
            <button @click="count++;$emit('increment', count)">点击了{{count}}次</button>
        </div>
        `,
    });
    Vue.component('posts', {
        template: '<div>文章内容</div>'
    });
    var vm = new Vue({
        el: '#app',
        data: {
            tab: 'home',
            count: 0
        },
    });
</script>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151423886.gif)

看代码可以知道父组件的count会在子组件count更新后变为子组件的count值。但我们切换组件后再切换回来，会发现<u>父组件count没变，子组件count变为初始值</u>。这是因为，每次切换组件都会创建当前组件的新实例。

那怎么保存先前组件的状态呢？

Vue提供了`keep-alive`组件。我们使用该元素将要缓存的组件包裹起来。让我们看看效果：

```html
<keep-alive>
    <component :is="tab" @increment="count=$event"></component>
</keep-alive>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151423879.gif)

成功解决问题！

`<keep-alive>`的注意事项：

- `<keep-alive>`本身不会渲染为DOM元素。
- 当`<keep-alive>`内组件切换时，会执行activated和deactivated两个生命周期钩子函数。
- *`<keep-alive>`不会在函数式组件中正常工作，因为它们没有缓存实例*。
- *被切换的组件需要设置name选项*。

# 异步组件

在大型应用中，我们可能需要将应用分割成小一些的代码块，并且只在需要的时候才从服务器加载一个模块。

Vue简化了这个过程的实现。

Vue允许我们以工厂函数的形式定义组件。

```javascript
Vue.component('async-component',function(resolve,reject){
    setTimeout(function(){
        resolve({
            template:'<div><h2>Async Component Content</h2></div>'
        });
    },1000);
});
```

工厂函数里可以异步地resolve来自服务器的组件定义对象。如果组件load failed，也可以调用reject(reason)。

这样定义的组件只有当它需要被render时才会被加载，而且会缓存渲染结果用于以后的重新渲染。

使用`Vue.component`方法结合webpack的code-splitting来定义一个factory函数：

```javascript
Vue.component('async-webpack-example', function (resolve) {
  // 这个特殊的 `require` 语法将会告诉 webpack
  // 自动将你的构建代码切割成多个包，这些包
  // 会通过 Ajax 请求加载
  require(['./my-async-component'], resolve)；
})；
```

我们也可以在factory函数中返回一个Promise，

```javascript
Vue.component(
    `async-webpack-example`,
    ()=>import('./my-async-component')
);
```

`()=>import('./my-async-component')`返回一个Promise，那么就可以根据Promise的状态来自动处理异步加载和渲染的过程。

> import() return value
>
> Returns a promise which fulfills to a module namespace object: an object containing all exports from moduleName.
>
> The evaluation of import() never synchronously throws an error. moduleName is coerced to a string, and if coercion throws, the promise is rejected with the thrown error.

局部注册异步组件：

```javascript
new Vue({
    components:{
        'my-component':()=>import('./my-async-component')
    }
})
```

# 处理加载状态

异步组件工厂函数可以返回一个对象，该对象包含有关异步组件加载、加载中、加载失败等情况的配置信息，这种方式允许你更加精细地控制异步组件地加载和渲染过程。下面是这种格式的异步组件配置对象的详细解释：

```javascript
const AsyncComponent=()=>({
    //需要加载的组件
    component:import('./MyComponent.vue'),
    //异步加载时使用的组件
    loading:LoadingComponent,
    //加载失败时使用的组件
    error:ErrorComponent,
    //展示加载时组件的延迟时间，默认值是200（毫秒）
    delay:200,
    // 如果提供了超时时间且组件加载也超时了，
    // 则使用加载失败时使用的组件。默认值是：`Infinity`，即没有超时限制
    timeout: 3000
});

Vue.component('async-component', AsyncComponent);
```

```html
<template>
  <div>
    <async-component></async-component>
  </div>
</template>
```



# 参考资料

- [keep-alive](https://v2.cn.vuejs.org/v2/api/#keep-alive)
- [Dynamic & Async Components](https://v2.vuejs.org/v2/guide/components-dynamic-async.html)
- [import()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import)
