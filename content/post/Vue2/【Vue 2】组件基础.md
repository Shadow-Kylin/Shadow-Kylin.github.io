---
title: 【Vue 2】组件基础
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
# 简单的组件示例

```html
<div id="app">
    <button-counter></button-counter>
</div>
<script>
    Vue.component('button-counter', {
        template: `<button v-on:click="addCount">你点击了我{{count}}次</button>`,
        data() {
            return {
                count: 0
            }
        },
        methods: {
            addCount() {
                this.count++;
            }
        },
    })
    var app = new Vue({
        el: '#app',
    })
</script>
```

- 组件是可任意次数复用的`Vue`实例，所以它与`new Vue()`接收相同选项，但是`data`必须是一个函数，以确保不同组件不会共享数据。
- `Vue.component`用于全局注册一个组件。
- 每个组件必需有且只有一个根元素。
- 全局注册组件必须在vue实例化之前，否则组件注册不成功。

# 使用事件抛出值

主要是`$emit`和`$event`

```html
//在子组件里使用
@eventName="$emit('customeEventName',thrown value)"

//在父组件里，如果下面是使用表达式，则可以通过 $event 获取 thrown value；如果使用函数的话，则该函数的第一个参数为 thrown value
<child-component @customeEventName="JS statement or a function"></child-component>
```

# 使用prop向子组件传值

注册子组件

```javascript
Vue.component("child-component",{
    //定义prop
    props:['propName'],
    template:
    `
    ...
    `
})
```

传值给子组件props数组里的prop

```html
<child-component propName="字符串"></child-component>
```

如果prop绑定的是动态值（比如data里的数据）那么就使用v-bind绑定

```html
<child-component v-bind:propName="动态值"></child-component>
```

# 在组件上使用v-model

最常见的：

```html
<input v-model="inputText">
```

它等价于：

```html
<input v-bind:value="inputText" v-on:input="inputText=$event.target.value">
```

如果是自定义组件，如custom-input：

```html
<custom-input v-model="inputText">
// 等价于
<custom-input v-bind:value="inputText" v-on:input="inputText=$event"></custom-input>
```

`v-bind:value="inputText"`实现了inputText改变，则value改变，

`v-on:input="inputText=$event"`实现了value改变，则inputText改变。

# 简单了解通过插槽分发内容


插槽用于将组件将内容分发到特定位置，换句话说，允许你在父组件中传递内容到子组件，并且子组件可以决定如何显示这些内容。

假如有一个custom-button组件，我们希望可以自定义其名称。

```javascript
Vue.component('custom-button',{
    template:
    `
    <button>
    <slot></slot>
  	</button>
    `
})
```

然后我们可以在父组件中这样传递内容：

```html
<custom-button>
  Click me!
</custom-button>
```

Click me!就会被插入到slot的位置。

# 初步了解动态组件

动态组件值在一个挂载点可以切换多个组件，类似于tab页的切换。

Vue使用`<component>`元素加上`is` attribute来实现动态组件，is可绑定的值包括已注册组件名或一个组件的选项对象。

一个动态组件的例子如下：

```html
<div id="app">
    <button @click="type=type==='first'?'second':'first'">{{type}}</button>
    <component :is="type"></component>
</div>
<script>
    Vue.component('first', {
        template: 
        `<div>
            <h1>1</h1>
        </div>`,
    })
    Vue.component('second', {
        template:
        `<div>
            <h1>2</h1>
        </div>`,
    })
    var app = new Vue({
        el: '#app',
        data: {
            type: 'first'
        }
    })
</script>
```

# 解析DOM模板时的注意事项

ul、ol、table、select这些元素只允许某些特定的元素出现在其内部，

如果像下面这样

```html
<table>
   	<child-component></child-component>
</table>
```

则会导致渲染出错

比如下面

```html
<div id="app">
    <table>
        <demo></demo>
    </table>
</div>
<script>
    Vue.component('demo', {
        template: 
        `
        <div>
            <h1>demo</h1>
        </div>
        `,
    })
    var app = new Vue({
        el: '#app',
    })
</script>
```

虽然显示结果与我们预期的一样，但是F12查看发现，demo组件内容被提到了table元素的上面：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/images/202308252312934.png)

对于这种情况，我们可以使用前文动态组件中介绍的is属性来解决：

```html
<div id="app">
    <table>
        <tr :is="type"></tr>
    </table>
</div>
<script>
    ...
    var app = new Vue({
        el: '#app',
        data: {
            type: 'demo'
        }
    })
</script>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/images/202308252312095.png)

需要注意的是如果我们从以下来源使用模板的话，这条限制是不存在的：

- 字符串 (例如：`template: '...'`)
- [单文件组件 (`.vue`)](https://v2.cn.vuejs.org/v2/guide/single-file-components.html)
- `<script type="text/x-template">`