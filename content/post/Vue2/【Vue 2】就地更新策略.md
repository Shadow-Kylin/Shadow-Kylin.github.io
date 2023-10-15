---
title: 【Vue 2】就地更新策略
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
在Vue中使用v-for渲染列表时，默认使用就地更新策略。该策略默认是基于索引的，规定在列表绑定的数据元素顺序变化时，不会重新创建整个列表，而只是更新对应DOM元素上的数据。以下代码实现了一个TODO列表的勾选、添加和删除功能：

```html
<!DOCTYPE html>
<html>

<head>
    <title>In-Place Update Example</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <ul>
            <li v-for="(todo, index) in todos" :key="index">
                <input type="checkbox" v-model="todo.completed">
                {{ todo.text }}
                <button @click="removeTodo(index)">Remove</button>
            </li>
        </ul>
        <button @click="addTodo">Add Todo</button>
    </div>

    <script>
        const app = new Vue({
            el: '#app',
            data: {
                todos: [
                    { text: 'Learn Vue.js', completed: false },
                    { text: 'Build an app', completed: true },
                    { text: 'Deploy to production', completed: false }
                ]
            },
            methods: {
                removeTodo(index) {
                    this.todos.splice(index, 1);
                },
                addTodo() {
                    this.todos.push({ text: 'New Todo', completed: false });
                }
            }
        });
    </script>
</body>

</html>
```

该策略模式是高效的，避免了大量的DOM重排重绘。

然而，该策略基于一个前提：列表项内部的内容不依赖于子组件的状态或临时的DOM状态。如违背该前提，就可能导致意外，因为Vue不会重新创建子组件或恢复临时DOM状态。

下面代码实现了v-for列表项内容依赖于子组件的状态而导致意外的情况：

```html
<!DOCTYPE html>
<html>

<head>
    <title>In-Place Update with Child Component</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <!-- 使用 v-for 渲染子组件列表 -->
        <child-component v-for="(item, index) in itemList" :key="index"
         @remove="removeItem(index)"></child-component>
    </div>

    <script>
        Vue.component('child-component', {
            template: `
        <div>
            <!-- 子组件的内容和状态 -->
            <button @click="increment">{{ count }}</button>
            <!-- 删除 -->
            <button @click="$emit('remove')">删除</button>
        </div>
      `,
            methods: {
                increment() {
                    this.count++;
                }
            },
            data(){
                return{
                    count:0
                }
            }
        });

        const app = new Vue({
            el: '#app',
            data: {
                itemList: new Array(5).fill(null)
            },
            methods: {
                removeItem(index) {
                    this.itemList.splice(index, 1);
                }
            }
        });
    </script>
</body>

</html>
```

我们先点击某项计数器，再删除该项：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151427698.gif)

为了解决该问题，我们为每一项绑定一个唯一的key属性：

```javascript
<!DOCTYPE html>
<html>

<head>
    <title>In-Place Update with Child Component</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <!-- 使用 v-for 渲染子组件列表 -->
        <child-component v-for="(item, index) in itemList" :key="item.id"
         @remove="removeItem(index)"></child-component>
    </div>

    <script>
        Vue.component('child-component', {
            template: `
        <div>
            <!-- 子组件的内容和状态 -->
            <button @click="increment">{{ count }}</button>
            <!-- 删除 -->
            <button @click="$emit('remove')">删除</button>
        </div>
      `,
            methods: {
                increment() {
                    this.count++;
                }
            },
            data(){
                return{
                    count:0
                }
            }
        });

        const app = new Vue({
            el: '#app',
            data: {
                // 为每一项添加一个id
                itemList: [
                    { id: 0 },
                    { id: 1 },
                    { id: 2 }
                ]
            },
            methods: {
                removeItem(index) {
                    this.itemList.splice(index, 1);
                }
            }
        });
    </script>
</body>

</html>
```

效果如下：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151427364.gif)

那么就有疑问了：为什么前面代码中的key属性绑定了index没有用呢，index难道不是唯一的吗？很简单，这是由于我们删除的是数据项，而不是数组索引，使用id就不会有这个问题，删除一项连带着删除了该唯一id。

