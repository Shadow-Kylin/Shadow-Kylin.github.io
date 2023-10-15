---
title: 【Vue 2】Slots
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
可以先阅读[组件基础-简单了解通过插槽分发内容](https://blog.csdn.net/2201_75288929/article/details/132277897?ops_request_misc=&request_id=757df8ffc84d4b89a0d0ef12ac93c067&biz_id=&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~koosearch~default-1-132277897-null-null.268^v1^control&utm_term=%E7%BB%84%E4%BB%B6%E5%9F%BA%E7%A1%80&spm=1018.2226.3001.4450#简单了解通过插槽分发内容)。

---

# 一、插槽定义

**插槽将子组件标签间的内容分发到子组件模板的`<slot>`标签位置**。

如果没有`<slot>`标签，那么该内容将被丢弃。

---

# 二、编译作用域

<u>内容在哪个作用域编译，就可以访问哪个作用域的数据</u>。

**1.父级模板的作用域**

在父组件的模板中定义的变量，可以在整个父组件的模板中访问，包括子组件中的插槽内容。

例如，我们在父组件中定义一个变量`url`，在子组件插槽内容中访问：

```vue
<template>
	<div>
        <navigation-link url="./index">
        	This URL is {{url}}
        </navigation-link>
    </div>
</template>
```

**2.子级模板的作用域**

在子组件中定义的数据和变量，只能在子组件中访问，而不能在父组件中访问。

---

# 三、后备内容

插槽的后备内容即**没有提供内容时的默认内容**。

定义方式很简单：

```vue
<slot>Slot的默认内容</slot>
```

---

# 四、具名插槽

顾名思义：具有名字的插槽。

你可以为插槽指定名称`name`，以便将内容分发到特定的插槽中。

接着，通过在`template`元素上使用`v-slot:插槽名参数`指令使用具名插槽：

完整示例：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue 2 具名插槽 Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <my-layout>
            <p>主要内容的一个段落。</p>
            <p>另一个主要段落。</p>
            <template v-slot:header>
                <h1>这里可能是一个页面标题</h1>
            </template>
            <template v-slot:footer>
                <p>这里是一些联系信息</p>
            </template>
        </my-layout>
    </div>
    <script>
        Vue.component('my-layout',{
            template:`
            <div>
                <header>
                    <slot name="header"></slot>
                </header>
                <main>
                	<!--默认插槽，隐含name为`default`-->
                    <slot></slot>
                </main>
                <footer>
                    <slot name="footer"></slot>
                </footer>
            </div>
            `
        })
        var vm = new Vue({
            el: '#app',
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/c46ad85dd6d14a5680365f250d580922.png)

虽然我们故意将默认插槽的内容放在了名为`header`的代码前面，但是结果依然按照插槽原本的顺序排列。

*一个组件中默认插槽只能有一个。*

---

**动态插槽名**

我们可以使用**动态参数**绑定插槽名。

```html
<template v-slot:[dynamicSlotName]></template>
```

---

**具名插槽的缩写**

`v-slot`指令也有缩写，即使用`#`替代`v-slot:`，这样做我们就必须明确给出其插槽名称。

---

# 五、作用域插槽

作用域插槽这个概念是指**让插槽内容能够访问子组件中才有的数据**。

我们只需给`<slot>`绑定prop，然后使用v-slot的值访问该`prop`。v-slot的值实际上是所有绑定的prop的集合。

上例子：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Vue 2 作用域插槽 Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <my-component>
            <template v-slot="slotProps">
                <p>{{slotProps.text}}</p>
            </template>
        </my-component>
    </div>
    <script>
        Vue.component('my-component', {
            template: `
            <div>
                <slot text="hello"></slot>
            </div>
            `
        })
        var vm = new Vue({
            el: '#app',
        });
    </script>
</body>

</html>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a814a3174ed94c8c8cc793e0f08a151e.png)

注意，上面例子中我们`v-slot`指令没有带参数，实际上它默认带了个`default`参数，**如果存在别的具名插槽，那么我就必须为`v-slot`指令带上参数**。

通过作用域插槽的机制，我们可以实现父组件控制插槽内容来控制子组件渲染不同的结果。

> **额外的不相关话题：使用`v-bind`指令时，什么时候加`:`？**
>
> 私以为：这取决于v-bind绑定的属性的值，其值为静态字符串，不加`:`，其值若为变量或对象字面量，则加`:`。

---

**解构插槽prop**

事实上，**插槽内容会被封装在一个函数内，`v-slot`的值就是其参数**。

作为函数参数，我们可以使用JavaScript的解构语法，这会使得代码更简洁。

**1.普通解构**

```html
<template v-slot="{text}">
    <p>{{text}}</p>
</template>
```

**2.重命名`prop`**

```html
<template v-slot="{text:msg}">
    <p>{{msg}}</p>
</template>
```

**3.指定后备内容**

```html
<template v-slot="{text={msg:'Hello'}}">
    <p>{{text.msg}}</p>
</template>
```