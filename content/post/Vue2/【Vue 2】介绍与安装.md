---
title: 【Vue 2】介绍与安装
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
# Vue.js是什么

Vue（读音 /vjuː/，类似于view）是一套用于构建用户界面的前端**渐进式JavaScript框架**，被设计为自底向上逐层应用。

> “渐进式”意味着可以按需引入Vue，而不必一次性全部采用。
>
> “自底向上逐层应用”意味着从底层开始构建，并逐渐添加层次化的功能。

Vue的核心库只关注视图层，易于上手和与第三方库或既有项目结合。

另一方面，当与现代化的工具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

> “现代化的工具链”通常包括构建工具、打包工具、模块化系统等，如Webpack、Babel，这些工具帮助开发者更加高效地编写、组织和部署代码。Vue可以与这些工具无缝结合使用。
>
> “类库”：诸如**状态管理库**（Vuex/Pinia）、**路由库**（Vue Router）、**UI组件库**（Element UI、Ant Design）等，Vue与这些库集成，可以构建出强大的单页应用。
>
> “单页应用”，Single Page Application，简写为SPA，SPA是一种现代化地Web应用程序，旨在在单个页面上提供与用户交互所需的全部功能，而无需刷新页面。

# 安装

**1.直接用`<script>`引入Vue**

我们可以先下载[开发版本](https://v2.cn.vuejs.org/js/vue.js)或[生产版本](https://v2.cn.vuejs.org/js/vue.min.js)(压缩版，删除了警告)的Vue，然后使用`<script>`标签引入，这样会注册一个全局`Vue`变量。

**2.使用CDN引入Vue**

CDN，全称Content Delivery Network，即内容分发网络。它可以让你在不下载和存储Vue.js文件的情况下使用Vue框架。

具体像下面这样使用：

```html
# 制作原型或学习
<script src="https://cdn.jsdelivr.net/npm/vue@2.7.14/dist/vue.js"></script>
# 用于生成环境
<script src="https://cdn.jsdelivr.net/npm/vue@2.7.14"></script>
# 使用原生ES Modules
<script type="module">
  import Vue from 'https://cdn.jsdelivr.net/npm/vue@2.7.14/dist/vue.esm.browser.js'
</script>
```

> 原生ES Modules是JavaScript中的一种模块化系统，在ES6中引入。ES Modules允许开发者将代码划分成独立模块，以便更好的组织代码、管理和复用代码。

Vue 也可以在 [unpkg](https://unpkg.com/vue@2.7.14/dist/vue.js) 和 [cdnjs](https://cdnjs.cloudflare.com/ajax/libs/vue/2.7.14/vue.js) 上获取 (cdnjs 的版本更新可能略滞后)。

**3.使用NPM引入Vue**

构建大型Vue项目时，建议使用NPM安装Vue。因为NPM能很好地和诸如Webpack和Browserify模块打包器配合使用，Vue也提供配套工具（如Vue-cli）来开发单文件组件。

```shell
# 最新稳定版
$ npm install vue
```

**4.命令行工具（CLI）**

详情可查阅 [Vue CLI 的文档](https://cli.vuejs.org/)。

**5.Bower**

Bower只提供UMD版本。

```shell
# 最新稳定版本
$ bower install vue
```

**6.AMD模块加载器**

AMD是浏览器端模块化开发的规范，需要用到对应的库函数`RequireJS`。

所有 UMD 版本都可以直接用作 AMD 模块。

# Vue兼容性

Vue不支持IE8及以下版本，因为Vue使用了IE8无法模拟的ECMAScript 5特性，它支持所有兼容ECMAScript 5的浏览器。

# 语义化版本控制

Vue在其所有项目中公布的功能和行为都遵循[语义化版本控制](https://semver.org/lang/zh-CN/)，对于未公布的或内部暴露的行为，其变更会描述在[发布说明](https://github.com/vuejs/vue/releases)中。

> “语义化版本控制”意味着每次发布新版本，版本号都会遵循一定规则进行更新，以表明版本变化的含义。语义化版本号的格式通常为MAJOR.MINOR.PATCH。
>
> - MAJOR：当进行不向后兼容（不兼容旧版本）的重大变更时添加。
> - MINOR：当添加新功能但保持向后兼容时增加。
> - PATCH：当进行向后兼容的Bug修复时添加。

# Vue Devtools

建议在浏览器安装上[Vue Devtools](https://github.com/vuejs/vue-devtools#vue-devtools)这个插件，它提供方便的功能和友好的界面来调试和优化Vue应用。具体地，Vue Devtools允许开发者在浏览器中查看Vue应用的组件层级、监视组件的状态变化、查看事件的出发情况、查看和编辑组件的数据等。

# 对不同构建版本的解释

在 [NPM 包的 `dist/` 目录](https://cdn.jsdelivr.net/npm/vue@2.7.14/dist/)你将会找到很多不同的 Vue.js 构建版本。

|                               | UMD                | CommonJS              | ES Module (基于构建工具使用) | ES Module (直接用于浏览器) |
| :---------------------------- | :----------------- | :-------------------- | :--------------------------- | -------------------------- |
| **完整版**                    | vue.js             | vue.common.js         | vue.esm.js                   | vue.esm.browser.js         |
| **只包含运行时版**            | vue.runtime.js     | vue.runtime.common.js | vue.runtime.esm.js           | -                          |
| **完整版 (生产环境)**         | vue.min.js         | -                     | -                            | vue.esm.browser.min.js     |
| **只包含运行时版 (生产环境)** | vue.runtime.min.js | -                     | -                            | -                          |

术语解释：

1. 完整版：同时包含编译器和运行时的版本。
2. 编译器：用来将模板字符串编译成JavaScript渲染函数的代码。
3. 运行时：用来创建Vue实例、渲染并处理虚拟DOM等的代码。基本上就是除去编译器的一切。在生产环境中，通常使用运行时版本，因为模板编译通常在构建阶段完成。
4. UMD：全称Universal Module Define，UMD 版本可以通过 `<script>` 标签==直接用在浏览器==中。jsDelivr CDN 的 https://cdn.jsdelivr.net/npm/vue@2.7.14 默认文件就是运行时 + 编译器的 UMD 版本 (`vue.js`)。
5. **[CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)**：CommonJS 版本用来配合==老的打包工具==比如 [Browserify](http://browserify.org/) 或 [webpack 1](https://webpack.github.io/)。这些打包工具的默认文件 (`pkg.main`) 是只包含运行时的 CommonJS 版本 (`vue.runtime.common.js`)。
6. **[ES Module](http://exploringjs.com/es6/ch_modules.html)**：从 2.6 开始 Vue 会提供两个 ES Modules (ESM) 构建文件：
   - 为打包工具提供的 ESM：为诸如 [webpack 2](https://webpack.js.org/) 或 [Rollup](https://rollupjs.org/) 提供的现代打包工具。ESM 格式被设计为可以被静态分析，所以打包工具可以利用这一点来进行“tree-shaking”并将用不到的代码排除出最终的包。为这些打包工具提供的默认文件 (`pkg.module`) 是只有运行时的 ES Module 构建 (`vue.runtime.esm.js`)。
   - 为浏览器提供的 ESM (2.6+)：用于==在现代浏览器中通过 `<script type="module">` 直接导入==。

# 与自定义元素的关系

Vue支持与自定义元素进行集成，可以通过以下方法将Vue组件构建成原生的自定义元素，从而让Vue组件能够在支持Cutome Element的环境中使用：

1.安装`vue-custome-element`库：

```shell
npm install vue-custome-element
```

2.在Vue组件中注册自定义元素：

```js
import Vue from 'vue'
import vueCustomeElement from 'vue-custome-element'
import MyComponent from './MyComponent.vue'

//使用vue-custome-element插件
Vue.use(vueCustomeElement)

//注册自定义元素
Vue.customElement('my-custome-element',MyComponent);
```

[其他demo](https://karol-f.github.io/vue-custom-element/#/demos/basic)。

在Vue3中有原生API支持自定义元素。

Vue CLI也支持将Vue组件构建成为原生的自定义元素。

> Vue CLI是Vue官方提供的一个命令行工具，用于快速搭建Vue项目，在Vue CLI中，可以使用`vue-cli-service build --target wc --name my-element src/App.vue`命令将`App.vue`组件构建成自定义元素，并指定名称为`my-elemnt`。