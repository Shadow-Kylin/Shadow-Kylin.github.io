---
title: 【JavaScript】Promise
description: 
slug: 
date: 2023-10-10
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
# 同步与异步的概念

JavaScript 是一门单线程的语言，这意味着它在任何给定的时间只能执行一个任务。

然而，JavaScript 通过异步编程技术来处理并发操作，以避免阻塞主线程的情况。

![在这里插入图片描述](https://img-blog.csdnimg.cn/6467dde9b81e451187bc4a8202385893.png)

在上图中，同步行为的进程 A 因为等待进程 B 执行完而被阻塞了一段时间。异步行为的进程 A 则会继续执行，等到进程 B 有了结果，它再告知进程 A 来处理。

异步行为是为了优化计算量大而耗时长的操作，但也并非只能处理该类情况，只要需要执行某个异步操作且不想主线程被阻塞，那么都可以使用异步编程。异步行为类似于系统中断。

同步行为对应内存中顺序执行的处理器指令，指令执行完后就容易推断出程序的状态，每个操作都是可预测性的。

设计一个这样的异步系统是困难的，因为你不知道异步结果什么时候可以获取。

# JavaScript 最初的异步编程方式：回调

使用回调作为异步编程的方式：回调函数作为另一个函数的参数，并在某个事件发送或异步操作完成后执行。

```javascript
function fetchData(callback){
	setTimeout(function(){
		callback('Data fetched');
	},1000);
}

fetchData(function(result){
	console.log(result);
});
```

加上对失败回调的处理：

```javascript
function fetchData(successCallback, errorCallback) {
  setTimeout(function () {
    // 模拟一个错误，你可以根据具体情况处理错误
    const error = null; // 这里假设没有错误
    if (error) {
      errorCallback(error); // 调用失败回调函数并传递错误
    } else {
      successCallback('Data fetched'); // 调用成功回调函数并传递数据
    }
  }, 1000);
}

// 使用 fetchData 函数
fetchData(
  function (data) {
    console.log('Success:', data);
  },
  function (error) {
    console.error('Error:', error);
  }
);
```

这种模式有很多弊端：首先需要在指定时间内才能得到异步函数的返回值，其次需要提前定义好回调函数。

最后，多个异步操作嵌套在一起，会形成**回调地狱**。

```javascript
function fetchData(callback) {
    setTimeout(function () {
        callback("Data fetched");
    }, 1000);
}

function processData(data, successCallback, errorCallback) {
    setTimeout(function () {
        // 模拟一个错误
        const error = null; // 这里假设没有错误
        if (error) {
            errorCallback(error);
        } else {
            successCallback("Data processed");
        }
    }, 1000);
}

function saveData(data, successCallback, errorCallback) {
    setTimeout(function () {
        // 模拟一个错误
        const error = new Error("Save failed");
        errorCallback(error);
    }, 1000);
}

fetchData(function (data) {
    processData(
        data,
        function (processedData) {
            saveData(
                processedData,
                function () {
                    console.log("Data saved successfully");
                },
                function (error) {
                    console.error("Error saving data:", error);
                }
            );
        },
        function (error) {
            console.error("Error processing data:", error);
        }
    );
});
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/4cb6bdc558004de2968648fb223b135c.png)

嵌套的回调难以阅读和维护。

# 新时代：期约-Promise

期约是对尚不存在结果的一个替身。

期约提供了一种更清晰和可维护的方式来处理异步操作，避免了回调地狱的问题。

期约是基于 [Promises/A+](https://promisesaplus.com/) 规范建立的。

## 期约的状态

Promise 是 ECMAScript6 新增的引用类型，是一个具有状态的对象。

它有如下三种状态：

1. 待定-`pending`。期约最初始的状态。
2. 兑现-`fullfilled`。也可以称为 解决-`resolved`。
3. 拒绝-`rejected`。

![在这里插入图片描述](https://img-blog.csdnimg.cn/e74e4f01bf0e48e8ad0b98e27adde1d8.png)

*状态一经改变，不可修改*。

期约的状态是私有的，只能在内部进行操作，不能被外部代码检测和修改。
## 初始化期约-new Promise(executor)

使用`XMLHttpRequest`模拟异步操作创建期约：

```javascript
// 建立请求，创建期约的工厂函数
function makeRequest(url) {
    return new Promise((resolve, reject) => {
        // 异步操作
        const xhr = new XMLHttpRequest();
        xhr.open("GET", url);
        xhr.onload = function () {
            if (xhr.status >= 200 && xhr.status < 300) {
                // 请求成功，将响应文本作为成功结果
                resolve(xhr.responseText);
            } else {
                // 请求失败，将错误信息作为失败原因
                reject("请求失败，状态码: " + xhr.status);
            }
        };
        xhr.onerror = function () {
            // 请求错误，将错误信息作为失败原因
            reject("网络错误");
        };
        xhr.send();
    });
}

const myPromise = makeRequest("https://example.com/api/data");
```

使用 new 创建 Promise 实例时，需要传入一个执行器（executor）函数作为参数，该函数接受两个参数：`resolve` 和 `reject`。

前面提到期约的状态只能在内部操作，这个操作就是在执行器函数中完成的。

`resolve` 会将 Promise 状态切换为 `fullfilled`，`reject`则会将其切换为 `rejected`。同时，调用 `reject` 会抛出错误。

执行器函数是期约的初始化程序，且是同步执行的，当初始化期约时就已经改变了期约的状态。


## 期约的构造器方法|静态方法之二
### Promise.resolve()

`Promise.resolve`是一个静态方法，它返回一个已解决（fulfilled）的Promise对象，并可以选择将一个值解析为成功的结果。

如果传递给`Promise.resolve`的值本身已经是一个Promise对象，则它会保持不变（不会再次解析）。

```javascript
setTimeout(console.log, 0, Promise.resolve());
setTimeout(console.log, 0, Promise.resolve(1));

const p = new Promise(() => {});
setTimeout(console.log, 0, Promise.resolve(p));
setTimeout(console.log, 0, p === Promise.resolve(p));
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/eeb1340def0f45d1b1b6573753d0714e.png)

### Promise.reject()

`Promise.reject`是一个静态方法，用于创建一个已拒绝（rejected）的Promise对象，并指定一个原因（通常是一个错误对象）作为拒绝的原因。

与`Promise.resolve`不同，`Promise.reject`不会解析传递给它的值，而是将其作为拒绝原因直接传递给Promise对象。

```javascript
setTimeout(console.log, 0, Promise.reject());
setTimeout(console.log, 0, Promise.reject(1));

const p = new Promise(() => {});
setTimeout(console.log, 0, Promise.reject(p));
setTimeout(console.log, 0, p === Promise.reject(p));
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a3af7c44f5b9414b8cca746fdad9cb86.png)

注意到，错误被抛出但没有被捕获(`Uncaught`)。我们给它套上`try...catch`试试。

```javascript
try {
    setTimeout(console.log, 0, Promise.reject());
} catch (e) {
    console.log(e);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6e5c6d1327144524bc209a75e375f18b.png)

这就奇怪了，为什么还是没有捕获到错误呢？

因为`try..catch`只能捕获同步代码中的错误，它位于当前执行栈中，而`Promise.reject`会被推入微任务队列，当当前执行栈执行完后，再执行它。

要和异步代码交互，只能使用期约的实例方法：

- Promise.prototype.then
- Promise.prototype.catch
- Promise.prototype.finally

## 期约的实例方法

*期约的实例方法是连接外部同步代码和内部异步代码的桥梁*。

任何暴露的异步结构，或者叫做期约的实例方法中都实现了一个`then()`方法。这个方法被认为实现了一个`Thenable`接口。

### Promise.prototype.then

`Promise.prototype.then`是Promise对象的一个实例方法，用于附加回调函数来处理Promise的解决（fulfilled）和拒绝（rejected）状态。

```javascript
promise.then(onFulfilled, onRejected)
```

`then`方法返回一个新的 Promise 对象，该对象有以下几种情况：

1. 如果`onFulfilled`或`onRejected`返回一个值（不是Promise），则返回的新Promise将以该值解决。
2. 如果`onFulfilled`或`onRejected`抛出异常，则返回的新Promise将以该异常作为原因拒绝。注意返回错误对象会把该错误对象包装在一个解决的期约中。
3. 如果`onFulfilled`或`onRejected`返回一个Promise，则返回的新Promise将与该返回的Promise具有相同的状态和结果。

下面是一个示例：

```javascript
const promise = new Promise((resolve, reject) => {
    // 模拟异步操作
    setTimeout(() => {
        const randomNumber = Math.random();
        if (randomNumber < 0.5) {
            resolve(`成功：${randomNumber}`);
        } else {
            reject(`失败：${randomNumber}`);
        }
    }, 1000);
});

promise.then(
    (result) => {
        console.log(`成功回调：${result}`);
    },
    (error) => {
        console.error(`失败回调：${error}`);
    }
);
```

`then`方法返回一个新的`Promise`对象，那么就可以链式调用它。

```javascript
// 模拟延迟
function delay(ms) {
    return new Promise((resolve) => {
        setTimeout(resolve, ms);
    });
}

function fetchUserData() {
    return delay(1000).then(() => {
        return { username: "john_doe", email: "john@example.com" };
    });
}

function fetchUserPosts(username) {
    return delay(1000).then(() => {
        return ["Post 1", "Post 2", "Post 3"];
    });
}

function displayUser(username, posts) {
    console.log(`Username: ${username}`);
    console.log("Posts:");
    posts.forEach((post, index) => {
        console.log(`${index + 1}. ${post}`);
    });
}

fetchUserData()
    .then((user) => {
        console.log("Fetching user data...");
        console.log(user);
        return fetchUserPosts(user.username);
    })
    .then((posts) => {
        console.log("Fetching user posts...");
        console.log(posts);
        displayUser("john_doe", posts);
    })
    .catch((error) => {
        console.error("Error:", error);
    });
```

输出：

```
Fetching user data...
{ username: 'john_doe', email: 'john@example.com' }
Fetching user posts...
[ 'Post 1', 'Post 2', 'Post 3' ]
Username: john_doe
Posts:
1. Post 1
2. Post 2
3. Post 3
```

`then`的链式调用避免了回调地狱，提高了代码的可维护性。

### Promise.prototype.catch

虽然`then`方法已经可以为期约添加拒绝处理程序：`promise.then(null,onRejected)`，但是这样不是很美观，于是就有了语法糖：`promise.catch(onRejected)`。

行为上与then是一致的。这里不再细究。

### Promise.prototype.finally

`finally`方法用于给程序添加`onFinally`回调函数，该回调无论Promise对象是fulfilled还是rejected都会执行。

```javascript
const promise = new Promise((resolve, reject) => {
    // 模拟异步操作
    setTimeout(() => {
        const randomNumber = Math.random();
        if (randomNumber < 0.5) {
            resolve(`成功：${randomNumber}`);
        } else {
            reject(`失败：${randomNumber}`);
        }
    }, 1000);
});

promise
    .then(
        (result) => {
            console.log(`成功回调：${result}`);
        },
        (error) => {
            console.error(`失败回调：${error}`);
        }
    )
    .finally(() => {
        console.log("不管成功或失败，都会执行这里的回调");
    });
```

大多数情况下，调用`finally`会原样返回期约。

如果期约是待定的且在`onFinally`处理程序中抛出错误或返回了一个拒绝期约，则会返回一个拒绝期约。

这个方法避免了`then`和`catch`的`onFufilled`和`onRejected`中出现重复冗余代码。

## 期约的非重入non-reentrancy特性

当一个期约进入"落定状态"（settled state）时，与该状态相关的处理程序（例如，`.then`或`.catch`中的回调函数）不会立即执行，而是会被排入执行队列，等待事件循环处理。

一旦Promise对象进入了已成功或已失败状态，它就被认为是"落定"，不再处于悬挂状态。在这个状态下，Promise的结果或错误已经确定，不会再发生变化。

*与附加处理程序相关的同步代码（即添加处理程序的代码之后的代码）会在处理程序执行之前优先执行。*

*这个特性的存在是为了确保期约处理程序的执行不会中断当前执行的同步代码块。*

```javascript
Promise.resolve().then(() => console.log("onResolved 执行"));
console.log("同步代码执行");
```

输出

```
同步代码执行
onResolved 执行
```

更为精彩的示例：

```javascript
// 创建一个Promise
const myPromise = new Promise((resolve, reject) => {
    console.log("Promise开始执行");
    // 模拟异步操作
    setTimeout(() => {
        resolve("成功"); // 期约进入落定状态
    }, 2000);
    console.log("resolve() 返回");
});

console.log("Promise创建完成");

// 添加处理程序
myPromise.then((result) => {
    console.log(`处理程序执行，结果为：${result}`);
});

console.log("处理程序添加完成");

// 同步代码
console.log("同步代码执行");
```

输出

```
Promise开始执行
resolve() 返回
Promise创建完成
处理程序添加完成
同步代码执行
处理程序执行，结果为：成功
```

哪怕你把期约状态变化的代码的封装放在添加处理程序之后，结果也是一样：

```javascript
let synchronousResolve;
// 创建一个Promise
const myPromise = new Promise((resolve, reject) => {
    synchronousResolve = function () {
        console.log("Promise开始执行");
        // 模拟异步操作
        setTimeout(() => {
            resolve("成功"); // 期约进入落定状态
        }, 2000);
        console.log("resolve() 返回");
    }
});

console.log("Promise创建完成");

// 添加处理程序
myPromise.then((result) => {
    console.log(`处理程序执行，结果为：${result}`);
});

synchronousResolve();

console.log("处理程序添加完成");

// 同步代码
console.log("同步代码执行");
```

## 邻近处理程序的执行顺序

当给一个期约（Promise）添加多个处理程序（例如，`.then()`、`.catch()`、`.finally()`），这些处理程序会按照它们被添加的顺序依次执行。

```javascript
const myPromise = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("成功");
    }, 2000);
});

myPromise
    .then((result) => {
        console.log(`第一个处理程序执行，结果为：${result}`);
    })
    .then(() => {
        console.log("第二个处理程序执行");
    })
    .catch((error) => {
        console.error(`捕获到错误：${error}`);
    })
    .finally(() => {
        console.log("无论如何都会执行的finally处理程序");
    });
```

输出

```
第一个处理程序执行，结果为：成功
第二个处理程序执行
无论如何都会执行的finally处理程序
```
## 传递解决值和拒绝理由

当一个Promise对象进入"已成功"或"已失败"的落定状态后，它会提供相应的解决值（如果成功）或拒绝理由（如果失败）给与之相关联的处理程序。这些解决值和拒绝理由会作为函数参数传递给处理程序，从而允许进一步操作这些值。

1. 当一个Promise成功（通过调用`resolve()`）时，解决值会传递给`.then()`处理程序，允许您进一步操作这个值。
2. 当一个Promise失败（通过调用`reject()`）时，拒绝理由会传递给`.catch()`处理程序，允许您处理错误情况。
3. *在执行Promise的执行器函数中，解决值和拒绝理由是作为`resolve()`和`reject()`函数的第一个参数传递的。*
4. `Promise.resolve()`和`Promise.reject()`方法在被调用时也可以接收解决值和拒绝理由，并返回一个已经处于相应状态的Promise对象。这些值将会传递给与之关联的处理程序。

```javascript
function fetchDataFromServer(url) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            const success = Math.random() < 0.7;
            if (success) {
                const data = { id: 1, name: "John Doe" };
                resolve(data); // 请求成功，传递数据
            } else {
                reject("网络请求失败"); // 请求失败，传递错误信息
            }
        }, 1000);
    });
}

fetchDataFromServer("https://example.com/api/data")
    .then((response) => {
        console.log("成功获取数据：", response);
        // 在这里可以对获取到的数据进行操作
        return response.name; // 返回一个新值
    })
    .catch((error) => {
        console.error("网络请求错误：", error);
        // 在这里可以处理网络请求失败的情况
        throw new Error("处理失败"); //不会阻止代码执行
    })
    .then((value) => {
        console.log("在第二个.then()中获取的值：", value);
    })
    .catch((error) => {
        console.error("在第二个.catch()中获取的错误：", error.message);
    });

console.log("网络请求已发出");
```

图1

![在这里插入图片描述](https://img-blog.csdnimg.cn/1cd906349e5b4b5b85083ad89c74ffae.png)

图2

![在这里插入图片描述](https://img-blog.csdnimg.cn/b7d85085f6924819b35ca02c1fdff003.png)


## 期约连锁形成Promise链

前文提到过`then`的链式调用，也被称为**期约连锁**，`then`、`catch`和`finally`都返回一个新期约对象，链式调用它们就会形成一条**期约链**。

```javascript
function delay(ms, str) {
    return new Promise((resolve) => {
        console.log(str);
        setTimeout(resolve, ms);
    });
}
delay(1000, "promise 1")
    .then(() => delay(1000, "promise 2"))
    .then(() => delay(1000, "promise 3"))
    .then(() => delay(1000, "promise 4"));
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ea88ba22311046ff8b4917c54a62ce82.gif)

### 期约图的概念

一个期约可能对应多个处理程序。每个处理程序也可能返回一个新期约。

这就可能形成期约图。

期约图中，一个期约是一个节点，期约的处理程序则是对应期约的不同边。

```javascript
let A = new Promise((resolve, reject) => {
    console.log("A");
    resolve();
});
let B = A.then(() => console.log('B'));
let C = A.then(() => console.log('C'));
B.then(() => console.log('D'));
B.then(() => console.log('E'));
C.then(() => console.log('F'));
C.then(() => console.log('G'));
```

如上代码形成如下有向非循环的期约图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/7a078e1c7c1449c5863f3bd7b0722b02.png)

## 期约合成的静态方法

期约合成是指将多个期约组合成一个期约。
### Promise.all 

`Promise.all` 方法创建的期约会在一组期约全部解决后再解决，即并行执行多个异步操作，并在所有操作都成功完成时才返回结果，如果其中任何一个失败，`Promise.all` 方法立即返回失败的期约。

```javascript
Promise.all(iterable);
```

`Promise.all` 接受一个可迭代对象作为参数。可迭代对象的元素会通过 `Promise.resolve` 转换为期约。

```javascript
function createPromise() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            const randomnNumber = Math.random();
            (randomnNumber < 0.5 && resolve(`成功: ${randomnNumber}`)) || reject(`失败: ${randomnNumber}`);
        }, 1000);
    });
}
let p = Promise.all([createPromise(), createPromise()]);
console.log(p);
p.then(() => {
    console.log("all resolved");
}).catch(() => {
    console.log("one rejected");
});
```
### Promise.race

和`Promise.all`一样，`Promise.race`接受一个可迭代对象作为参数。可迭代对象的元素会通过`Promise.resolve`转换为期约。

不同的是，`race`表示“竞争；角逐”的意思，`Promise.race`的可迭代对象的元素转换后的期约谁先settled就返回谁。

# 参考资料

- [JavaScript高级程序设计（第4版）](https://baike.baidu.com/item/JavaScript%E9%AB%98%E7%BA%A7%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%EF%BC%88%E7%AC%AC4%E7%89%88%EF%BC%89?fromModule=lemma_search-box)
