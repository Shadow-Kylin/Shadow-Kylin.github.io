---
title: 【Vue 2】nextTick方法、异步更新、事件循环
description: 
slug: 
date: 2023-09-03
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
# 1 nextTick的用处

`vm.$netTick`的作用是**将回调延迟到下次DOM更新周期之后执行**。

它接受一个回调函数作为参数。

**其实，在我们更新数据状态后，是不会立马渲染的，你不能即刻获取到新的DOM**：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Async Update Example</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
    <style>
        .font-color{
            color: red;
        }
    </style>
</head>

<body>
    <div id="app">
        <div ref="textBox1">{{text}}</div>
        <button @click="changeText">改变文本</button>
        <div ref="textBox2" :class="{'font-color': true}">null</div>
    </div>
    <script>
        new Vue({
            el: '#app',
            data: {
                text: "Today is a cloudy day."
            },
            methods: {
                changeText() {
                    this.text = this.text === "Today is a sunny day." ?
                        "Today is a cloudy day." : "Today is a sunny day.";
                    this.$refs.textBox2.innerHTML = this.$refs.textBox1.innerHTML;
                }
            }
        });
    </script>
</body>

</html>
```

![动画](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151410992.gif)

此时，我们可以使用nextTick方法：

```javascript
changeText(){
    this.text=this.text=== "Today is a sunny day."?
    "Today is a cloudy day.": "Today is a sunny day.";
    this.$nextTick(function(){
        this.$refs.textBox2.innerHTML = this.$refs.textBox1.innerHTML;
    });
}
```

![动画](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151410244.gif)

如果没有提供回调且在支持 Promise 的环境中，则返回一个 Promise。

```javascript
changeText(){
    this.text=this.text=== "Today is a sunny day."?
    "Today is a cloudy day.": "Today is a sunny day.";
    _this=this;
    this.$nextTick().then(function(){
        _this.$refs.textBox2.innerHTML = _this.$refs.textBox1.innerHTML;
    });
}
```

测试中我们发现上面代码中then的回调参数里的this指向了window，所以我们在外面使用`_this`。

# 2 异步更新机制

Vue侦测到数据变化时会通知到对应依赖管理器里的所有Watcher，然后虚拟DOM会对整个组件进行差异比较来更新DOM，Vue进行重新渲染。

如果我在一个循环中不停改变一个数据属性，那对应的Watcher就会收到多份通知，是不是要进行多次渲染呢？

明显不会，**Vue.js会将收到的watcher实例添加到异步更新队列中，且不会重复添加同一个watcher，然后等到下一次事件循环，一次性清空队列里的所有watcher并让它们触发渲染**。

# 3 事件循环

## 3.1 什么是事件循环

前面提到的事件循环又是什么？

我们知道，JavaScript是一门单线程且非阻塞的语言。

**单线程**意味着一次只能执行一个任务，也叫做主线程。

**非阻塞**意味着遇到**异步任务**（比如网络请求、文件读取、定时器等）时，JavaScript会将这些异步任务挂起，继续执行后面的代码。当异步任务处理完毕后，根据一定的规则（通常是回调函数或Promise）来处理操作的结果。

**挂起**（pending）是指将异步任务放入一个队列里，称为**事件队列**。

而异步任务可以分为**微任务**和**宏任务**。

微任务放在微任务队列，宏任务放在宏任务队列。

**当主线程执行栈的任务都执行完后，检查微任务队列，执行微任务的回调事件，直到微任务队列为空，再检查宏任务队列，从中选出一个事件，将其回调加入执行栈，重复上述步骤**。

这也就是**事件循环**。

## 3.1 常见微任务

**1）Promise的then、catch和finally**

当一个Promise的状态从pending变为fulfilled或reject时，与之相关的then或catch回调就会被添加到微任务队列。

```javascript
console.log("开始");

// 创建一个Promise对象
const myPromise = new Promise((resolve, reject) => {
    console.log("Promise中的同步代码");
    resolve("Promise成功"); // 模拟成功情况
});

// 添加then、catch和finally回调
myPromise
    .then((result) => {
        console.log("then回调执行:", result);
    })
    .catch((error) => {
        console.error("catch回调执行:", error);
    })
    .finally(() => {
        console.log("finally回调执行");
    });

console.log("Promise后的同步代码");

// 模拟宏任务
setTimeout(() => {
    console.log("宏任务回调执行");
}, 0);

console.log("结束");
```

输出

```javascript
开始
Promise中的同步代码
Promise后的同步代码
结束
then回调执行: Promise成功
finally回调执行
宏任务回调执行
```

**2）async/await**

await后面的表达式会生成一个微任务。

```javascript
console.log("开始");

// 模拟一个异步函数，返回一个Promise
async function fetchData() {
    console.log("异步函数内部的同步代码");
    return "Promise成功"; // 模拟成功情况
}

// 使用async/await来处理异步操作
async function processData() {
    try {
        const result = await fetchData(); // 等待Promise解决
        console.log("成功:", result);
    } catch (error) {
        console.error("失败:", error);
    } finally {
        console.log("finally回调执行");
    }
}

// 调用async函数
processData();

console.log("异步函数后的同步代码");

// 模拟宏任务
setTimeout(() => {
    console.log("宏任务回调执行");
}, 0);

console.log("结束");
```

输出

```
开始
异步函数内部的同步代码
异步函数后的同步代码
结束
成功: Promise成功
finally回调执行
宏任务回调执行
```

**3）MutationObserver回调**

MutationObserver是一个监视DOM树变化的API。

```javascript
<!DOCTYPE html>
<html>

<head>
    <title>MutationObserver示例</title>
    <style>
        .text{
            color: red;
        }
    </style>
</head>

<body>
    <div id="target">
        <p>这是一个段落。</p>
    </div>

    <script>
        // 选择要监视的目标元素
        const target = document.getElementById('target');

        // 创建一个MutationObserver实例，传入回调函数
        const observer = new MutationObserver(function (mutationsList, observer) {
            // 遍历变化列表中的每个MutationRecord
            for (let mutation of mutationsList) {
                if (mutation.type === 'childList') {
                    // 子节点变化
                    console.log('子节点变化：', mutation.addedNodes, mutation.removedNodes);
                } else if (mutation.type === 'attributes') {
                    // 属性变化
                    console.log('属性变化：', mutation.target.className, mutation.oldValue);
                }
            }
        });

        // 配置MutationObserver选项，监视子节点变化
        const config = { childList: true, attributes: true, subtree: true, characterData: true };

        // 开始观察目标元素
        observer.observe(target, config);

        // 在一段时间后，修改目标元素，观察变化
        setTimeout(function () {
            target.innerHTML = '<p>这是一个新的段落。</p>';
        }, 1000);

        // 属性变化
        setTimeout(function () {
            target.classList.add('text')
        }, 2000);
    </script>
</body>

</html>
```

**4）process.nextTick**

node.js中进程相关的对象

```javascript
console.log('这是第一个任务');
process.nextTick(function() {
  console.log('这是下一个微任务');
});
console.log('这是第二个任务');
```

**5）queueMicrotask**

这是一个ECMAScript 2020引入的方法。

```javascript
queueMicrotask(function() {
  // 这是一个微任务
});
```

## 3.2 常见宏任务

1）定时器任务，如`setTimeout`或`setInterval`

2）网络请求。

```javascript
const xhr = new XMLHttpRequest();
xhr.open('GET', 'https://example.com/api/data', true);
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4 && xhr.status === 200) {
    console.log('网络请求完成');
  }
};
xhr.send();
```

3）DOM操作，对DOM元素进行操作，例如添加、删除、修改元素。

DOM操作可能会导致页面的重新渲染和重排（reflow），这些操作是昂贵的，需要消耗较多的计算资源，因此它们通常被视为宏任务，而不是微任务。

你可以考虑使用 `requestAnimationFrame` 或其他微任务机制来优化DOM操作的性能。

4）文件操作

```javascript
const fs = require('fs');
fs.readFile('example.txt', 'utf8', function(err, data) {
  if (err) throw err;
  console.log('读取文件完成');
});
```

# 4 nextTick源码

```typescript
import { noop } from 'shared/util'
import { handleError } from './error'
import { isIE, isIOS, isNative } from './env'

//指示是否正在使用微任务来处理异步操作
export let isUsingMicroTask = false

//存储待执行的回调函数
const callbacks: Array<Function> = []
//是否有待执行的回调
let pending = false

//用于执行回调函数。它将 callbacks 数组中的回调函数依次执行，并在执行后清空 callbacks 数组
function flushCallbacks() {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}

//根据环境选择使用微任务或宏任务来执行回调函数
let timerFunc

//如果浏览器支持原生的 Promise，则使用 Promise 来实现微任务。
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    //在 iOS 上使用 UIWebView 时，添加一个空的 setTimeout(noop) 来强制刷新微任务队列
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (
  !isIE &&
  typeof MutationObserver !== 'undefined' &&
  (isNative(MutationObserver) ||
    MutationObserver.toString() === '[object MutationObserverConstructor]')
) {//如果浏览器不支持原生 Promise，但支持 MutationObserver，则使用 MutationObserver 来实现微任务
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  //这种情况下，timerFunc 将观察一个文本节点的字符数据变化来触发回调函数的执行
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else {
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
```

