---
title: 【Vue 2】过渡
description: 
slug: 
date: 2023-09-12
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
# 前言

Vue 提供了多种方式来实现过渡效果。

1. 在CSS过渡和动画中自动应用class
2. 配合CSS动画库
3. 在过渡钩子函数中使用JavaScript操作DOM
4. 配合JavaScript动画库

# 单元素/组件的过渡

将元素或组件放在\<transition>中可以在下列情形中触发过渡效果：

1. 使用了v-if
2. 使用了v-show
3. 使用了动态组件
4. 组件根节点

如果没有找到JavaScript过渡钩子和CSS过渡/动画，DOM操作在下一帧中立即执行。

# 过渡的类名

Vue提供了6个可以自动生成的CSS类名，如下图。

![](https://v2.cn.vuejs.org/images/transition.png)

可以自动生成的类名是指给transition组件的name属性指定一个值，假设是fade，那么该name将自动扩展生成 .fade-enter、.fade-enter-active、.fade-enter-to、.fade-leave、.fade-leave-active、.fade-leave-to等上述6个类，并应用在transition组件里的过渡元素或组件上。

示例：

```css
<style>
    .fade-enter {
        opacity: 0;
        transform: translateY(-20px);
    }

    .fade-enter-active {
        transition: all 0.3s ease;
    }

    .fade-enter-to {
        opacity: 1;
        transform: translateY(0);
    }

    .fade-leave {
        opacity: 1;
    }

    .fade-leave-active {
        transition: opacity 0.5s ease;
    }

    .fade-leave-to {
        opacity: 0;
    }
</style>
```

```html
<div id="app">
    <button @click="showElement=!showElement">显示/隐藏元素</button>
    <transition name="fade">
        <div v-if="showElement">
            你好
        </div>
    </transition>
</div>
<script>
    new Vue({
        el: '#app',
        data: {
            showElement: false
        },
    });
</script>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151436592.gif)

# 自定义过渡类名

如果想结合使用[animate.css](https://animate.style/)动画库，可以在transition组件使用以下props来指定类名：

| enter-class | enter-active-class | enter-to-class | leave-class | leave-active-class | leave-to-class |
| ----------- | ------------------ | -------------- | ----------- | ------------------ | -------------- |

示例：

```html
<div id="app">
    <button @click="showElement=!showElement">显示/隐藏元素</button>
    <transition
        enter-active-class="animated fadeInLeft"
        leave-active-class="animated fadeOut">
        <div v-if="showElement">
            你好
        </div>
    </transition>
</div>
<script>
    new Vue({
        el: '#app',
        data: {
            showElement: false
        },
    });
</script>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151436368.gif)

[Animate 动画演示](https://www.njqxwl.com/upload/animate/index.html)

# 指定要监听的事件类型

通过设置transition组件的type属性来指定要监听的过渡事件类型（transitionend 和 animationend）。

type值可以是transition或animation。

示例：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue 2 过渡 Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.14/dist/vue.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">
    <style>
        .fade-enter {
            opacity: 0;
            transform: translateY(-20px);
        }
    
        .fade-enter-active {
            transition: all 0.3s ease;
        }
    
        .fade-enter-to {
            opacity: 1;
            transform: translateY(0);
        }
    
        .fade-leave {
            opacity: 1;
        }
    
        .fade-leave-active {
            transition: opacity 0.5s ease;
        }
    
        .fade-leave-to {
            opacity: 0;
        }
    </style>
</head>

<body>
    <div id="app">
        <button @click="toggleElement">Toggle Element</button>
        <transition name="fade"
            type="transition"
            >
            <div v-if="showElement" key="element">Hello, Vue!</div>
        </transition>
        <transition name="bounce"
            enter-active-class="animated bounceIn"
            leave-active-class="animated bounceOut"
            type="animation"
            >
            <div v-if="showElement" key="element">Hello, vue!</div>
        </transition>
    </div>
    <script>
        new Vue({
            el: '#app',
            data: {
                showElement: false
            },
            methods: {
                toggleElement() {
                    this.showElement = !this.showElement;
                },
            },
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151436794.gif)

# 指定过渡持续时间

默认情况下，Vue会等待过渡所在根元素的第一个`transitionend`或`animationend`事件。

我们可以使用 transition组件上的duration prop定制一个显示的过渡持续时间，以毫秒为单位。

```html
<transition :duration="1000">...</transition>

<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```

# transition组件上的JavaScript钩子函数

共8个：

| before-enter | enter | after-enter | enter-cancelled | before-leave | leave | after-leave | leave-cancelled |
| ------------ | ----- | ----------- | --------------- | ------------ | ----- | ----------- | --------------- |

注意点：

- 只用JavaScript过渡时，在leave和enter中必须使用done参数来告诉Vue何时过渡已经完成。
- 推荐对于仅使用JavaScript过渡的元素添加`v-bind:css="false"`，Vue会跳过CSS的检测。这也可以避免过渡过程中CSS的影响。

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue 2 过渡 Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.14/dist/vue.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">
    <!--
        Velocity 和 jQuery.animate 的工作方式类似，也是用来实现 JavaScript 动画的一个很棒的选择
    -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>
    </style>
</head>

<body>
    <div id="app">
        <button @click="toggleElement">切换元素</button>
        <transition name="custom-transition" @before-enter="beforeEnter" @enter="enter" @leave="leave">
            <div v-if="showElement" key="element">Hello, Vue!</div>
        </transition>
    </div>
    <script>
        new Vue({
            el: '#app',
            data: {
                showElement: false
            },
            methods: {
                toggleElement() {
                    this.showElement = !this.showElement;
                },
                beforeEnter(el) {
                    // 在进入过渡开始之前调用
                    el.style.opacity = 0;
                },
                enter(el, done) {
                    // 在进入过渡的主要阶段调用
                    Velocity(el, { opacity: 1, translateY: '0px',fontSize: '2em' }, { duration: 1000, complete: done });
                    Velocity(el, { fontSize: '1em' }, { complete: done })
                },
                leave(el, done) {
                    // 在离开过渡的主要阶段调用
                    Velocity(el, { opacity: 0, translateY: '20px' }, { duration: 1000, complete: done });
                },
            }
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151436862.gif)

# 初始渲染的过渡

启用初始渲染的过渡：

```html
<transition
	appear
	appear-class=""
	appear-active-class=""
	appear-to-class=""
	v-on:before-appear=""  
	v-on:appear=""  
	v-on:after-appear=""  
	v-on:appear-cancelled=""
	>
</transition>
```

# 多个元素的过渡

如果想实现多个原生元素的切换过渡，可以使用[条件渲染](https://blog.csdn.net/2201_75288929/article/details/132699219)的相关指令。

如果切换的元素标签名相同，比如都是button，那么最好给它们设置唯一的`key`属性，否则可能出现过渡效果不生效或没按设置过渡的情况。

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue 2 过渡 Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.14/dist/vue.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">
    <!--
        Velocity 和 jQuery.animate 的工作方式类似，也是用来实现 JavaScript 动画的一个很棒的选择
    -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>
    </style>
</head>

<body>
    <div id="app">
        <button @click="toggleElement">切换元素</button>
        <transition @before-enter="beforeEnter" @enter="enter" @leave="leave">
            <div v-if="showElement" key="el1">Hello, girls!</div>
            <div v-else key="el2">Hello, boys!</div>
        </transition>
    </div>
    <script>
        new Vue({
            el: '#app',
            data: {
                showElement: false
            },
            methods: {
                toggleElement() {
                    this.showElement = !this.showElement;
                },
                beforeEnter(el) {
                    // 在进入过渡开始之前调用
                    el.style.opacity = 0;
                },
                enter(el, done) {
                    // 在进入过渡的主要阶段调用
                    Velocity(el, { opacity: 1, translateY: '0px',fontSize: '2em' }, { duration: 1000, complete: done });
                    Velocity(el, { fontSize: '1em' }, { complete: done });
                },
                leave(el, done) {
                    // 在离开过渡的主要阶段调用
                    Velocity(el, { opacity: 0, translateY: '20px' }, { duration: 1000, complete: done });
                },
            }
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151437238.gif)

也可以使用绑定动态property的方式来替代使用了多个v-if的相同标签名元素。

```html
<transition>
	<div v-if="show==='el1'" key="el1">el1</div>
	<div v-if="show==='el2'" key="el2">el2</div>
</transition>
```

替换成

```html
<transition>
	<div :key="el">el</div>
</transition>
```

# 过渡模式

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151439199.gif)

如上图中，前一个元素的过渡结束时，另一个元素的过渡开始，这有时候并不是我们想要的效果。

于是，Vue提供了过渡模式。

```html
<transition name="fade" mode="out-in">
</transition>
```

mode可以为以下值：

- `in-out`：新元素先过渡进入，旧元素后过渡离开。
  ![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151439611.gif)
- `out-in`：旧元素先过渡离开，新元素再过渡进入。
  ![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151439791.gif)

So cool!

# 多个组件的过渡

多个元素我们可以使用key attribute，对于多个组件，我们可以使用更为简单的is attribute，即动态组件。

```html
<transition name="component-fade" mode="out-in">
  <component v-bind:is="view"></component>
</transition>
```

# 列表过渡

前面所讲都是针对单个节点或者多个节点中的一个。

而对于有多个节点的列表的渲染，我们使用`transition-group`组件。

`transition-group`组件的特点：

1. *与`transition`不同，它以一个真实的元素存在，默认为span，可以使用tag attribute修改。*
2. *不能使用过渡模式。*
3. *列表元素都需要key attribute。*
4. CSS过渡的类会自动应用在列表内部元素。

# 列表的进入/离开过渡

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue 2 过渡 Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.14/dist/vue.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">
    <!--
        Velocity 和 jQuery.animate 的工作方式类似，也是用来实现 JavaScript 动画的一个很棒的选择
    -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>
    </style>
    <style>
        .list-enter-active,
        .list-leave-active {
            transition: opacity,transform 0.5s;
        }

        .list-enter,
        .list-leave-to {
            opacity: 0;
            transform: translateX(60px);
        }

        .list-item {
            list-style:decimal-leading-zero;
            margin: 5px;
            padding: 5px;
            background-color: #f0f0f0;
            border: 1px solid #ddd;
            border-radius: 5px;
            width: fit-content;
        }
    </style>
</head>

<body>
    <div id="app">
        <button @click="addItem">Add Item</button>
        <button @click="removeItem">Remove Item</button>
        <transition-group name="list" tag="ul">
            <li v-for="item in items" :key="item.id" class="list-item">
                {{item.text}}
            </li>
        </transition-group>
    </div>
    <script>
        new Vue({
            el: '#app',
            data: {
                items: [],
                nextItemId: 0
            },
            methods: {
                addItem() {
                    let randomIndex=Math.floor(Math.random()*this.items.length);
                    this.items.splice(randomIndex,0, {
                        id: this.nextItemId++,
                        text: `Item ${this.nextItemId}`
                    });
                },
                removeItem() {
                    let randomIndex = Math.floor(Math.random()*this.items.length);
                    this.items.splice(randomIndex, 1);
                }
            },
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151439317.gif)

# 列表的排序过渡

不仅可以给进入/离开添加过渡，当列表的元素位置发生变化时，也可以产生过渡效果，通过`move-class`attribute自定义类名来实现，也可以使用`name`作为前缀。

例如当`name="list"`时：

```css
.list-move {
  transition: transform 1s;
}
```

这个可以解决列表过渡不够平滑的问题。

如果想给所有的变动都添加过渡动画，那么可以按以下方法做：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue 2 过渡 Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.14/dist/vue.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">
    <!--
        Velocity 和 jQuery.animate 的工作方式类似，也是用来实现 JavaScript 动画的一个很棒的选择
    -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.14.1/lodash.min.js"></script>
    <style>
        .list-enter,
        .list-leave-to {
            opacity: 0;
            transform: translateX(30px);
        }

        .list-leave-active{
            position: absolute;
        }

        .list-item {
            transition: all 1s;
            list-style: none;
            margin: 5px;
            padding: 5px;
            background-color: #f0f0f0;
            border: 1px solid #ddd;
            border-radius: 5px;
            width: fit-content;
        }
    </style>
</head>

<body>
    <div id="app">
        <button @click="addItem">Add Item</button>
        <button @click="removeItem">Remove Item</button>
        <button @click="shuffleItems">Shuffle Items</button>
        <transition-group name="list" tag="ul">
            <li v-for="item in items" :key="item.id" class="list-item">
                {{item.text}}
            </li>
        </transition-group>
    </div>
    <script>
        new Vue({
            el: '#app',
            data: {
                items: [],
                nextItemId: 0
            },
            methods: {
                addItem() {
                    let randomIndex = Math.floor(Math.random() * this.items.length);
                    this.items.splice(randomIndex, 0, {
                        id: this.nextItemId++,
                        text: `Item ${this.nextItemId}`
                    });
                },
                removeItem() {
                    let randomIndex = Math.floor(Math.random() * this.items.length);
                    this.items.splice(randomIndex, 1);
                },
                shuffleItems() {
                    this.items = _.shuffle(this.items);
                }
            },
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151439248.gif)

这被叫做[FLIP](https://aerotwist.com/blog/flip-your-animations/)过渡，使用这种过渡方式的元素不能设置为`display:inline`。

# 列表的交错过渡

通过 HTML 的[data](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/data-*)属性与 JavaScript 进行通信来实现交错排序。

```html
<li v-for="(item, index) in computedList"  
	v-bind:key="item.msg"  
	v-bind:data-index="index"  
>{{ item.msg }}</li>
```

```javascript
enter: function (el, done) {
  var delay = el.dataset.index * 150; // 通过 data-index 获取元素的索引，然后计算延迟时间
  setTimeout(function () {
    Velocity(
      el,
      { opacity: 1, height: '1.6em' }, // 使用 Velocity.js 库来应用 CSS 属性的动画效果
      { complete: done } // 动画完成后调用 done 回调函数来通知 Vue.js 过渡完成
    );
  }, delay);
},
```

每个元素都有不同的延迟，以实现交错的过渡效果。

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151439736.gif)

# 可复用的过渡

使用template ：

```javascript
Vue.component('my-special-transition', {
  template: '\
    <transition\
      name="reusable-transition"\
      mode="out-in"\
      v-on:before-enter="beforeEnter"\
      v-on:after-enter="afterEnter"\
    >\
      <slot></slot>\
    </transition>\
  ',
  methods: {
    beforeEnter: function (el) {
      // ...
    },
    afterEnter: function (el) {
      // ...
    }
  }
})
```

使用函数式组件：

```javascript
Vue.component('reusable-transition', {
  functional: true,
  render: function (createElement, context) {
    var data = {
      props: {
        name: 'reusable-transition',
        mode: 'out-in'
      },
      on: {
        beforeEnter: function (el) {
          // ...
        },
        afterEnter: function (el) {
          // ...
        }
      }
    }
    return createElement('transition', data, context.children)
  }
})
```

使用单文件组件：

```vue
<template>
  <transition name="fade">
    <slot></slot>
  </transition>
</template>

<script>
export default {
  name: 'ReusableTransition',
};
</script>

<style>
    /* 在这里定义过渡的 CSS 类名和样式 */
    .fade-enter-active, .fade-leave-active {
      transition: opacity 0.5s;
    }
    .fade-enter, .fade-leave-to /* .fade-leave-active 在版本 2.1.8 中可用 */ {
      opacity: 0;
    }
</style>
```

总之，可复用过渡就是以`transition`或`transition-group`作为根元素。

# 动态过渡

所有过渡attribute都可以动态绑定，那么可以使用不同的过渡效果。

而不仅仅改变过渡attribute可以进行动态过渡，在event hooks里改变组件上下文的数据也可以。

下面是绑定name属性达到的动态过渡效果。

```css
<style>
    .default-enter,
    .default-leave-to {
        opacity: 0;
        transform: translateX(30px);
    }

    .slide-enter,
    .slide-leave-to {
        transform: translateY(30px);
        opacity: 0;
    }

    .default-leave-active,
    .slide-leave-active{
        position: absolute;
    }

    .list-item {
        transition: all 1s;
    }
</style>
```

```html
<div id="app">
    <input v-model="newTodo" @keyup.enter="addTodo" placeholder="添加新任务">
    <button @click="toggleTransition">切换过渡方式</button>
    <transition-group :name="dynamicTransitionName" tag="ul">
        <li v-for="todo in todos" :key="todo.id" class="list-item">
            {{todo.text}}
            <button @click="removeTodo(index)">删除</button>
        </li>
    </transition-group>
</div>
<script>
    new Vue({
        el: '#app',
        data: {
            dynamicTransitionName: 'default',
            newTodo: '',
            todos: [],
            nextID: 0
        },
        methods: {
            addTodo() {
                if (this.newTodo.trim() !== '') {
                    this.todos.push({id:this.nextID++, text: this.newTodo });
                    this.newTodo = '';
                }
            },
            removeTodo(index) {
                this.todos.splice(index, 1);
            },
            toggleTransition(){
                this.dynamicTransitionName=this.dynamicTransitionName==='default'?'slide':'default';
            }
        },
    });
</script>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151439737.gif)

# 参考资料

- [进入/离开 & 列表过渡](https://v2.cn.vuejs.org/v2/guide/transitions.html)