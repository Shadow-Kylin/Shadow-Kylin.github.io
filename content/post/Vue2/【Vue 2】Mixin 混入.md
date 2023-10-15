---
title: 【Vue 2】Mixin
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
混入（Mixins）是一种在Vue组件中重用代码的方式。它允许你定义一些可复用的选项对象，然后将这些选项合并到不同的组件中。混入可以用于在多个组件之间共享逻辑、方法、生命周期钩子等。

示例：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue mixin demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <my-component></my-component>
    </div>
    <script>
        // 定义一个混入对象
        const myMixin = {
            data() {
                return {
                    message: 'Hello from mixin!'
                }
            },
            methods: {
                greet() {
                    console.log(this.message)
                }
            }
        }

        Vue.component('my-component', {
            mixins: [myMixin],
            template: '<div>{{message}}</div>',
            created() {
                this.greet()
            }
        })
        var vm = new Vue({
            el: '#app',
        });
    </script>
</body>

</html>
```



---

**选项合并**

当组件和混入对象的选项同名时，数据对象data中同名选项以组件的优先，进行递归合并；同名钩子函数则会被合并为数组，它们都会执行，且混入对象的钩子函数先执行。

对于methods、components、directives这些值为对象的选项，同名键的合并例子如下：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue mixin demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
    </div>
    <script>
        const mixin = {
            methods: {
                greet() {
                    console.log('Hello from Mixin!');
                },
                sayHello() {
                    console.log('Mixin says hello!');
                },
            }
        };

        var Component = Vue.extend({
            methods: {
                greet() {
                    console.log('Hello from extend!');
                },
                sayHello() {
                    console.log('extend says hello!');
                },
                sayGoodbye() {
                    console.log('extend says goodbye!');
                }
            }
        });

        new Component({
            mixins: [mixin],
            methods: {
                greet() {
                    console.log('Hello from component!');
                },
            },
            created() {
                this.greet();        // Output: Hello from component!
                this.sayHello();     // Output: Mixin says hello!
                this.sayGoodbye();   // Output: extend says goodbye!
            }
        });

    </script>
</body>

</html>
```

由上面得出选项合并优先级：<span style="color:red;font-weight:bold">new Component > mixins > Vue.extend</span>。

---

**全局混入**

<mark>全局混入会影响每一个Vue实例</mark>。

使用`Vue.mixin({})`方法创建全局混入。

尽量避免使用全局混入，这样会导致*逻辑分散、代码难以阅读、维护困难*。

---

⭐**自定义选项合并策略**

下面是一个示例：

```javascript
const customMergeStrategies=Vue.config.optionMergeStrategies
customMergeStrategies.customOption=function(parentVal,childVal){
    if(!childVal) return parentVal
    if(!parentVal) return childVal
    //合并逻辑，可根据需要修改
    return parentVal.concat(childVal)
}
```

关键是了解Vue.config.optionMergeStrategies的作用，它用于帮助我们自定义某个选项的合并策略函数，该函数第一个参数是父组件的该选项值，第二个参数是子组件的该选项值。