---
title: 【Vue 2】自定义指令
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
Vue 自定义指令允许我们在 DOM 元素上添加自己想要的行为来扩展 Vue 的功能。

<mark>一个自定义指令需要一个名称和一个定义对象</mark>。在定义对象中，你可以使用一些钩子函数来控制指令的行为：

1. <font color="red" size="5">bind</font>：在指令被绑定到元素上时使用，只调用一次。可以用来初始化一些值。
2. <font color="red" size="5">inserted</font>：在被绑定元素插入父节点时调用。可以用来执行初始的DOM操作，比如设置焦点/绑定事件。
3. <font color="red" size="5">update</font>：在被绑定元素的值更新时调用，无论绑定值是否改变。可以用来响应值的更新。可能发生在其子VNode更新之前。
4. <font color="red" size="5">componentUpdated</font>：指令所在组件的VNode及其子VNode全部更新后调用。
5. <font color="red" size="5">unbind</font>：指令与元素解绑时调用，清除绑定的一些事件监听器。

下面给出一个简单的示例：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue Custom Directive Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <input v-model="colorValue" v-change-color  style="width: 50px;"  />
    </div>
    <script>
        Vue.directive('change-color', {
            inserted: function (el) {
                el.addEventListener('input', function () {
                    el.style.color = el.value
                })
            }
        })
        var vm = new Vue({
            el: '#app',
            data: {
                colorValue: 'black'
            }
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/images/202308252311996.gif)

---

Vue自定义指令的钩子函数接受一些参数，这些参数提供了有关指令的上下文信息及对应的DOM元素：

1. bind(el,binding,vnode):
   - <font color="red" size="5">el</font>：绑定指令的元素。
   - <font color="red" size="5">binding</font>：一个对象，包含以下属性：
     - <font color="red" size="5">name</font>：指令名称，不包括`v-`前缀。
     - <font color="red" size="5">value</font>：指令的绑定值，可以是一个表达式或变量。
     - <font color="red" size="5">oldValue</font>：指令之前的绑定值。
     - <font color="red" size="5">expression</font>：绑定值的表达式字符串形式。
     - <font color="red" size="5">arg</font>：指令参数，例如`v-my-directive:arg`中的arg。
     - <font color="red" size="5">modifiers</font>：修饰符对象，例如`v-my-directive.modifier1.modifier2`中，修饰符对象为`{modifier1:true,modifier2:true}`。
   - <font color="red" size="5">vnode</font>：Vue编译生成的虚拟节点。
2. inserted(el,binding,vnode)
3. update(el,binding,vnode,oldVnode):
   - <font color="red" size="5">oldVnode</font>：之前的虚拟节点，用于比较更新。
4. componentUpdated(el,binding,vnode,oldVnode)
5. unbind(el,binding,vnode)

---

我们也可以使用`v-my-directive:[arg]="value"`的形式来使用动态参数。

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue Custom Directive with Dynamic Argument</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <p v-mydirective:[arg1]>Hello World</p>
    </div>
    <script>
        Vue.directive('mydirective', {
            bind: function(el, binding, vnode) {
                console.log(binding.arg);
                el.style[binding.arg] = '5px solid red';
            }
        });
        var vm = new Vue({
            el: '#app',
            data: {
                arg1: 'border'
            }
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/images/202308252311989.png)

---

**bind和update的函数简写**

同时定义bind和update而不考虑其他钩子函数：

```javascript
Vue.directive('mydirective',function(el,binding){
    //...
})
```