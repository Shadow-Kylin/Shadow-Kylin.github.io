---
title: Service Worker API
description: 
date: 2025-01-11 00:53:00+0800
image: 
categories:
    - Frontend
tags:
    - JavaScript
    - Web APIs
draft: 
hidden: 
math: 
weight:
slug:
license: true
---
# Service Worker 简介

Service Worker 是一种 Web 技术，是运行在用户浏览器背景中的 JavaScript 文件。它提供了拦截和处理网络请求（HTTPS 环境下）的能力，允许开发者缓存资源并创建离线体验，同时还能支持推送通知和后台数据同步等功能。

# Service Worker 的重要特性

## 独立线程

Service Worker 在浏览器的单独线程中运行，与主线程分离，不直接访问 DOM，但可以通过 postMessage 与主线程通信。

## 生命周期

- 安装（install）：当浏览器检测到新的 Service Worker 脚本时，进入安装阶段。
- 激活（activate）：新 Service Worker 安装完成后，清理旧缓存或其他不需要的资源。
- 运行（fetch 等事件）：处理网络请求或提供离线内容。

## 相关事件

（1）install 事件，在 Serive Worker 首次安装时触发，用于预缓存资源。

```javascript
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open('my-cache').then(cache => {
      return cache.addAll(['/index.html', '/styles.css', '/script.js']);
    })
  );
});
```

（2）activate 事件，在 Serive Worker 激活时触发，用于清理旧缓存。

（3）fetch 事件，拦截网络请求时触发，用于返回缓存资源或动态请求。

（4）push 事件，与 Push API 结合，实现实时通知。

（5）sync 事件，用于后台同步操作（需要注册 Background Sync）。

（6）notificationclick 事件，当用户点击通知时触发。

# Service Worker 主要 API

## event.waitUntil

event.waitUntil 是 Service Worker 中的一种关键方法，用于确保在事件的生命周期内，异步任务完成之前事件不会终止。

## event.respondWith

event.respondWith 是 Service Worker 中专门用于拦截和处理网络请求的 API。它允许开发者接管浏览器对资源的默认请求和响应行为，并根据需要返回缓存、网络请求或其他自定义响应。（与缓存策略挂钩）

## Cache API

Cache API 提供了对缓存资源的读写操作，主要用于实现离线支持。

（1）`caches.open(cacheName)`

打开或创建一个缓存对象。

```javascript
caches.open('my-cache').then(cache => {
  cache.addAll(['/index.html', '/styles.css', '/script.js']);
});
```

（2）`caches.match(request)`

查找是否存在与请求匹配的缓存。

```javascript
caches.match('/index.html').then(response => {
  if (response) {
    return response; // 返回缓存
  } else {
    return fetch('/index.html'); // 请求网络
  }
});
```


（3）`caches.keys()`

获取所有缓存名称。

```javascript
caches.keys().then(keys => {
  console.log(keys); // 输出缓存名称数组
});
```

（4）`caches.delete(cacheName)`

删除指定缓存。

```javascript
caches.delete('old-cache').then(success => {
  if (success) console.log('缓存删除成功');
});
```

## Fetch API

Fetch API 用于拦截网络请求并自定义响应。结合 Service Worker 的 fetch 事件使用。

```javascript
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(response => {
      return response || fetch(event.request); // 返回缓存或网络请求
    })
  );
});
```

## Clients API

Clients API 用于与 Service Worker 的页面或其他上下文通信。

（1）`clients.matchAll()`

获取与 Service Worker 关联的所有客户端（浏览器窗口或标签页）。

```javascript
clients.matchAll().then(clientList => {
  clientList.forEach(client => {
    console.log('客户端 URL:', client.url);
  });
});
```

（2）`clients.openWindow(url)`

打开一个新窗口或标签页。

```javascript
self.addEventListener('notificationclick', event => {
  event.waitUntil(clients.openWindow('/new-page'));
});
```

（3）client.postMessage(message)

向特定客户端发送消息。

```javascript
clients.matchAll().then(clientList => {
  clientList[0].postMessage({ message: 'Hello from Service Worker!' });
});
```

## Notification API

Notification API 用于显示通知，与 push 事件结合使用。

（1）显示通知

在 Service Worker 中触发通知显示。

```javascript
self.registration.showNotification('标题', {
  body: '这是通知的正文',
  icon: '/icon.png',
  badge: '/badge.png'
});
```

（2）通知事件

用户点击通知时触发，通常用于跳转或执行操作。

```javascript
self.addEventListener('notificationclick', event => {
  event.notification.close(); // 关闭通知
  event.waitUntil(clients.openWindow('/target-page'));
});
```

## Sync API

Sync API 用于在网络恢复时执行后台任务，例如发送离线数据。

（1）注册后台同步任务

需要在主线程注册任务。

```javascript
navigator.serviceWorker.ready.then(sw => {
  sw.sync.register('sync-tag');
});
```

（2）处理同步事件——监听 sync 事件

```javascript
self.addEventListener('sync', event => {
  if (event.tag === 'sync-tag') {
    event.waitUntil(
      // 执行后台任务
      fetch('/sync-endpoint', { method: 'POST', body: JSON.stringify({ key: 'value' }) })
    );
  }
});
```

## Service Worker 注册和控制 API

这些 API 在主线程中使用，用于注册和管理 Service Worker。

（1）`navigator.serviceWorker.register(scriptURL)`：注册 Service Worker 脚本。

```javascript
navigator.serviceWorker.register('/service-worker.js').then(registration => {
  console.log('Service Worker 注册成功:', registration);
});
```

（2）`navigator.serviceWorker.ready`：返回一个 Promise，解析为 Service Worker 的控制对象。

```javascript
navigator.serviceWorker.ready.then(sw => {
  console.log('Service Worker 已就绪');
});
```

（3）`navigator.serviceWorker.controller`：获取当前页面的控制 Service Worker。

```javascript
if (navigator.serviceWorker.controller) {
  console.log('当前页面由 Service Worker 控制');
}
```

## IndexedDB API

虽然不是 Service Worker 专属，但常与其结合用于存储离线数据。

（1）创建和使用数据库

```javascript
const request = indexedDB.open('my-database', 1);
request.onsuccess = event => {
  const db = event.target.result;
  console.log('数据库连接成功:', db);
};
```

（2）

# 使用 Service Worker 的步骤

## 创建 Service Worker 文件

创建一个名为 service-worker.js 的文件，这是 Service Worker 的主脚本。

```javascript
// 缓存版本
const CACHE_NAME = 'v1';

// 要缓存的资源，路径相对于当前目录
const CACHE_ASSETS = [
    '/',
    '/index.html',
    '/styles.css',
    '/app.js',
    '/logo.png',
];

// 安装事件：缓存静态资源
self.addEventListener("install", (event) => {
  console.log("Service Worker: Installing...");
  self.skipWaiting(); // 跳过等待，直接进入激活状态
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => {
      console.log("Service Worker: Caching Files");
      return cache.addAll(CACHE_ASSETS);
    })
  );
});

// 激活事件：清理旧的缓存
self.addEventListener("activate", (event) => {
  console.log("Service Worker: Activating...");
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames.map((cacheName) => {
          if (cacheName !== CACHE_NAME) {
            console.log("Service Worker: Clearing Old Cache");
            return caches.delete(cache);
          }
        })
      );
    })
  );
  self.clients.claim(); // 立即接管页面控制权
});

// 拦截请求并返回缓存资源
self.addEventListener('fetch', event => {
    console.log('Service Worker: Fetching', event.request.url);
    event.respondWith(
        caches.match(event.request).then(response => {
            // 如果资源在缓存中，则返回缓存，否则发起网络请求
            return response || fetch(event.request);
        })
    );
});
```

文件结构

```
/project
  ├── index.html
  ├── styles.css
  ├── app.js
  ├── logo.png
  └── service-worker.js
```

## 注册 Service Worker

在你的主 JavaScript 文件中（如 main.js），注册 Service Worker。

```javascript
if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
        navigator.serviceWorker
            .register('/service-worker.js') // 路径相对于根目录
            .then(registration => {
                console.log('Service Worker registered with scope:', registration.scope);
            })
            .catch(err => {
                console.error('Service Worker registration failed:', err);
            });
    });
}
```

## 确保 HTTPS 环境

Service Worker 只能在安全上下文（HTTPS 或 localhost）中工作。如果使用本地开发环境，可以用 http-server 或 lite-server 等工具运行本地服务器。

```bash
npx http-server
```

## 缓存策略

根据你的应用需求，可以选择不同的缓存策略。

### 缓存优先策略

如果优先使用缓存，可以修改 fetch 事件：

```javascript
self.addEventListener('fetch', event => {
    event.respondWith(
        caches.match(event.request).then(cachedResponse => {
            return cachedResponse || fetch(event.request).then(networkResponse => {
                // 将网络请求的响应缓存起来
                return caches.open(CACHE_NAME).then(cache => {
                    cache.put(event.request, networkResponse.clone());
                    return networkResponse;
                });
            });
        })
    );
});
```

### 网络优先策略

如果需要优先从网络获取最新数据：

```javascript
self.addEventListener('fetch', event => {
    event.respondWith(
        fetch(event.request)
            .then(networkResponse => {
                // 将响应数据缓存起来
                return caches.open(CACHE_NAME).then(cache => {
                    cache.put(event.request, networkResponse.clone());
                    return networkResponse;
                });
            })
            .catch(() => caches.match(event.request)) // 如果网络请求失败，则使用缓存
    );
});
```

### 离线回退（Offline Fallback）

当网络和缓存都不可用时，返回一个备用的离线页面。

```javascript
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(response => {
      return response || fetch(event.request).catch(() => {
        return caches.match('/offline.html'); // 返回离线页面
      });
    })
  );
});
```

### 动态缓存更新

每次从网络获取资源时，更新缓存中的旧资源。

```javascript
self.addEventListener('fetch', event => {
  event.respondWith(
    fetch(event.request)
      .then(networkResponse => {
        return caches.open('dynamic-cache').then(cache => {
          cache.put(event.request, networkResponse.clone()); // 更新缓存
          return networkResponse; // 返回网络响应
        });
      })
      .catch(() => caches.match(event.request)) // 如果网络失败则使用缓存
  );
});
```

## 更新 Service Worker

当你需要更新 Service Worker 的脚本时，浏览器会检测文件的变化，并进入新的安装阶段。
为了让新版本立即生效，可以在 activate 事件中强制控制权交给新版本：

```javascript
self.addEventListener('activate', event => {
    event.waitUntil(self.clients.claim()); // 立即控制页面
});
```

## 调试与测试

1. 检查 Service Worker 状态： 在浏览器的开发者工具中，进入 Application -> Service Workers 查看状态。
2. 模拟离线模式： 在开发者工具中启用离线模式，测试应用是否能在离线状态下正常运行。
3. 清理缓存： 使用 caches.delete() 手动清理旧缓存。

## 结合 Push API 实现推送通知

Service Worker 的 push 事件和 Push API 是实现 Web 应用实时通知的核心技术。

当服务器向用户设备发送推送消息时，Service Worker 会监听到 push 事件。

### Push API 工作流程

1.订阅用户（订阅 Push Service）：用户同意接收推送通知后，浏览器通过 Push API 向 Push Service 注册订阅，发送一个包含订阅信息的对象，通常包括 endpoint（推送地址）和 public key（公钥）。

2.发送推送消息：服务器使用订阅对象中的 endpoint 和加密信息（使用 Web Push Protocol）发送消息。

3.接收推送消息：浏览器接收消息，并通过触发 Service Worker 的 push 事件将消息交给 Web 应用处理。

### 实现步骤

（1）客户端：订阅 Push Service

用户同意接收通知后，订阅 Push Service。

```javascript
// 注册 Service Worker
navigator.serviceWorker.register('/service-worker.js').then(registration => {
  console.log('Service Worker 注册成功:', registration);

  // 请求用户授权推送通知
  return Notification.requestPermission().then(permission => {
    if (permission === 'granted') {
      // 订阅 Push Service
      return registration.pushManager.subscribe({
        userVisibleOnly: true, // 必须设置为 true
        applicationServerKey: urlBase64ToUint8Array('<Your-VAPID-Public-Key>') // 服务器公钥
      });
    } else {
      console.log('通知权限未被授予');
    }
  });
}).then(subscription => {
  console.log('订阅成功:', JSON.stringify(subscription));
  // 将 subscription 发送到服务器
});
```

（2）服务器：发送推送消息

服务器使用 Web Push 库（如 web-push）发送消息。

```javascript
const webPush = require('web-push');

// VAPID 密钥
const vapidKeys = {
  publicKey: '<Your-VAPID-Public-Key>',
  privateKey: '<Your-VAPID-Private-Key>'
};

webPush.setVapidDetails(
  'mailto:example@yourdomain.com',
  vapidKeys.publicKey,
  vapidKeys.privateKey
);

// 推送订阅对象
const subscription = {
  endpoint: '<Browser-Endpoint>',
  keys: {
    p256dh: '<User-Public-Key>',
    auth: '<User-Auth-Key>'
  }
};

// 发送推送消息
webPush.sendNotification(subscription, JSON.stringify({
  title: '实时通知',
  body: '这是一条测试通知！',
  url: '/some-url'
})).then(response => {
  console.log('推送成功:', response);
}).catch(error => {
  console.error('推送失败:', error);
});
```

（3）Service Worker 处理 push 事件

在 Service Worker 中监听 push 事件，并显示通知。

```javascript
self.addEventListener('push', event => {
  console.log('收到推送消息:', event);

  let data = {};
  if (event.data) {
    data = event.data.json(); // 解析推送消息
  }

  const title = data.title || '默认标题';
  const options = {
    body: data.body || '默认消息',
    icon: '/icon.png',
    badge: '/badge.png',
    data: data.url || '/' // 点击通知后跳转的 URL
  };

  event.waitUntil(
    self.registration.showNotification(title, options)
  );
});
```

（4）notificationclick 事件：通知点击处理

监听通知的点击事件，控制点击行为（如跳转页面或执行操作）。

```javascript
self.addEventListener('notificationclick', event => {
  console.log('通知被点击:', event);

  const url = event.notification.data;
  event.notification.close(); // 关闭通知

  // 打开或跳转到指定页面
  event.waitUntil(
    clients.matchAll({ type: 'window' }).then(clientList => {
      for (const client of clientList) {
        if (client.url === url && 'focus' in client) {
          return client.focus();
        }
      }
      if (clients.openWindow) {
        return clients.openWindow(url);
      }
    })
  );
});
```

## 后台同步

通过 Background Sync API 实现可靠的后台任务。
