---
title: 【Vue 2】计算属性与侦听器
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
# 计算属性 vs 方法 vs 侦听器

计算属性的出现是为了解决模板内表达式太过复杂而变得难以维护。

假设我们知道长和宽，要计算一个矩形的面积，如果没有计算属性，我们可能像下面这样处理：

```javascript
<div id="app">
    <input type="text" v-model="width">
    <input type="text" v-model="height">
    <p>面积是{{area()}}</p>
</div>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            width:0,
            height:0
        },
        methods: {
            area: function() {
                return this.width * this.height
            }
        },
    })
</script>
```

使用计算属性：

```html
<div id="app">
    <input type="text" v-model="width">
    <input type="text" v-model="height">
    <p>面积是{{area}}</p>
</div>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            width:0,
            height:0
    	},
        computed:{
            area:function(){
                return this.width*this.height
            }
        }
    })
</script>
```

它们的区别是，*使用methods需要每次获取值都调用该方法，使用computed会将值缓存，只在它的响应式依赖变化时才会重新计算*。

侦听器watch和计算属性一样都会监听数据的变化，但是，对于要监听的属性，侦听器需要给它们每一个都单独设置响应函数，计算属性则只需按需设置，例如上面的例子使用侦听器的话：

```html
<div id="app">
    <input type="text" v-model="width">
    <input type="text" v-model="height">
    <p>面积是{{area}}</p>
</div>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            width:0,
            height:0,
            area:0
        },
        watch:{
            width:function(newVal){
                this.area = newVal * this.height
            },
            height:function(newVal){
                this.area = newVal * this.width
            }
        }
    })
</script>
```

这样会显得重复冗余。

所以，*计算属性适用于基于一种或多种属性计算出派生值的情况，例如计算面积、价格等，也适用于需要进行一些复杂的数据处理和转换；侦听器适用于监听属性的变化，然后执行一些异步操作（例如API调用）、更新其他属性或自定义逻辑（例如数据变化时的动态效果）*。

# 计算属性的getter与setter

计算属性默认是getter，我们也可以设置其setter。

```html
<div id="app">
    <p>Base Count: {{ baseCount }}</p>
    <p>Current Count: {{ count }}</p>
    <button @click="incrementCount">Increment Count</button>
    <button @click="incrementBaseCount">Increment Base Count</button>
</div>

<script>
    new Vue({
        el: '#app',
        data: {
            baseCount: 5
        },
        computed: {
            count: {
                // Getter function
                get: function () {
                    return this.baseCount * 2;
                },
                // Setter function
                set: function (newCount) {
                    this.baseCount = newCount / 2;
                }
            }
        },
        methods: {
            incrementCount: function () {
                this.count++;
            },
            incrementBaseCount: function () {
                this.baseCount++;
            }
        }
    });
</script>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151435437.gif)

# 参考资料

- [计算属性和侦听器](https://v2.cn.vuejs.org/v2/guide/computed.html)
