---
title: 【Vue 2】组件注册
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
# 1 组件名的命名规则

定义组件名的两种方式：

1. 短横线分隔命名，Kebab Case，例如`my-component-name`。
2. 单词首字母大写命名，Pascal Case，例如`MyComponentName`。

第一种方式在模板中使用`<my-component-name>`引用该元素，第二种方式可以使用`<my-component-name>`或`<MyComponentName>`。

---

# 2 全局注册和局部注册

在[组件基础](https://blog.csdn.net/2201_75288929/article/details/132277897)一文中我们提到`Vue.component`这种组件注册方式是全局注册的。

**全局注册**意味着它们可以在其他Vue实例中直接使用。

但有时对于某个全局注册的组件，我们并不是很频繁地使用，或者我们不再需要这个组件，但它仍会被包含在构建结果中。

于是我们可以采用**局部注册**的方式。

**不采用模块系统的局部注册**：

```html
<div id="app">
    <component-a></component-a>
</div>
<script>
    var ComponentA = {
        template: '<div>Component A</div>'
    };
    new Vue({
        el: '#app',
        components: {
            'component-a': ComponentA
        },
    });
</script>
```

如果你通过 Babel 和 webpack 使用 ES2015 模块，可以像下面这样进行局部注册：

```javascript
import ComponentA from './ComponentA'

export default{
    components:{
        ComponentA //ComponentA:ComponentA的缩写
    }
}
```

当然，在模块化系统中，我们也可以进行全局注册，除了使用之前提到的Vue.component，还可以在使用了webpack或Vue CLI 3+的前提下，使用`require.context`进行全局注册：

main.js

```javascript
import Vue from 'vue'
import App from './App.vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent=require.context(
	'./components',//基础组件相对路径
    false,//是否递归查询其子目录
    /Base[A-Z]\w+.(vue|js)$/ //基础文件名的正则表达式
)

requireComponent.keys().forEach(fileName=>{
    //获取组件配置
    const compoentConfig=requireComponent(fileName)
    //获取组件的PascalCase命名
    const componentName=upperFirst(//大写首字母
        camelCase(//转换字符串string为驼峰写法
            //获取与目录深度无关的文件名
            fileName
            .split('/')
            .pop()
            .replace(/\.\w+$/,'')
        )
    )
    //全局注册组件
    Vue.component(
        componentName,
        // 如果这个组件选项是通过 `export default` 导出的，
        // 那么就会优先使用 `.default`，
        // 否则回退到使用模块的根。
        componentConfig.default || componentConfig
    )
})

//阻止Vue在启动时生成生产提示，如下：
//You are running Vue in development mode.
//Make sure to turn on production mode when deploying for production.
//See more tips at https://vuejs.org/guide/deployment.html
Vue.config.productionTip=false

new Vue({
    //从App提供的模板编译渲染函数
    //h代指createElement方法，接收根组件App来创建VNode
    render:h=>h(App)
}).$mount('#app') //手动挂载
```

`Vue.component`用于全局注册单个组件，上面方法用于全局注册多个组件，所以会多次调用`Vue.component`方法。

---

**额外话题**

作者发现了[Vue.config.productionTip=false在某些位置不生效的情况](https://ask.csdn.net/questions/7989152?weChatOA=weChatOA1)。

实际上观察Vue检查devtools插件安装情况和开发模式的类型的源码部分可发现：

```javascript
var inBrowser = typeof window !== "undefined";

....
....

// devtools global hook
/* istanbul ignore next */
if (inBrowser) {
    setTimeout(function () {
        if (config.devtools) {
            if (devtools) {
                devtools.emit('init', Vue);
            } else {
                console[console.info ? 'info' : 'log'](
                    'Download the Vue Devtools extension for a better development experience:\n' +
                    'https://github.com/vuejs/vue-devtools'
                );
            }
        }
        if (config.productionTip !== false &&
            typeof console !== 'undefined'
        ) {
            console[console.info ? 'info' : 'log'](
                "You are running Vue in development mode.\n" +
                "Make sure to turn on production mode when deploying for production.\n" +
                "See more tips at https://vuejs.org/guide/deployment.html"
            );
        }
    }, 0);
}
```

`Vue.config.productionTip=false`应该在`inBrowser`为`true`时就已经被设置好，而`inBrowser`表示当前是否是浏览器环境，它通过检测`window`对象是否存在来判断，`window`对象在页面被渲染后定义。所以，`Vue.config.productionTip`设置时机是在页面渲染前。`body`中的`script`标签是在`body`解析完后才执行的。换言之，`Vue.config.productionTip`设置时机是在`body`解析前。