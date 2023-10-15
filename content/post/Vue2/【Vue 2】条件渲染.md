---
title: 【Vue 2】条件渲染
description: 
slug: 
date: 2023-09-05
image: 
categories:
    - Frontend
tags:
    - Vue 2
---
**条件渲染相关的指令有哪些？**

1. v-if、v-else、v-else-if
2. v-show

---

**v-if 的作用**

```html
<div v-if="expression"></div>
```

v-if 根据表达式 expression 返回的值是否为 truthy 来决定其内容是否被渲染。

Vue还实现了 v-else 和 v-else-if：

```html
<div v-if="expression1"></div>
<div v-else-if="expression2"></div>
<div v-else></div>
```

注意，**v-else-if 须紧跟在 v-if 后，v-else须紧跟在 v-if 或 v-else-if 后**。

如果想使用 v-if 控制多个元素的渲染，可以使用 \<template> 元素，因为它不会被包含在渲染结果内。

---

**v-if 相关源码**

首先是 src\compiler\parser\index.ts ：

处理 HTML 元素上的 if 系列指令，获取它们的表达式 exp ,如果是 v-if 指令的话，还要将其添加到它的 ifConditions 数组。

```typescript
function processIf(el) {
  const exp = getAndRemoveAttr(el, 'v-if')
  if (exp) {
    el.if = exp
    addIfCondition(el, {
      exp: exp,
      block: el
    })
  } else {
    if (getAndRemoveAttr(el, 'v-else') != null) {
      el.else = true
    }
    const elseif = getAndRemoveAttr(el, 'v-else-if')
    if (elseif) {
      el.elseif = elseif
    }
  }
}
```

getAndRemoveAttr 从元素中移除某个 attr ，

```typescript
// note: this only removes the attr from the Array (attrsList) so that it
// doesn't get processed by processAttrs.
// By default it does NOT remove it from the map (attrsMap) because the map is
// needed during codegen.
export function getAndRemoveAttr(
  el: ASTElement,
  name: string,
  removeFromMap?: boolean
): string | undefined {
  let val
  if ((val = el.attrsMap[name]) != null) {
    const list = el.attrsList
    for (let i = 0, l = list.length; i < l; i++) {
      if (list[i].name === name) {
        list.splice(i, 1)
        break
      }
    }
  }
  if (removeFromMap) {
    delete el.attrsMap[name]
  }
  return val
}
```

以后看懂了其他源码再回来补充😉

---

**v-if 和 v-for一起使用时的注意点**

当 `v-if` 和 `v-for` 一起使用时，确实存在一个优先级关系：`v-for` 具有比 `v-if` 更高的优先级。这意味着 `v-for` 指令将首先对数据进行迭代，然后在每个迭代项上应用 `v-if` 条件。

因此，如果你的本意是先进行 v-if 的判断，那么可以在循环外套个 \<template> 元素，把 v-if 写在 \<template> 元素上。

---

**使用 key attribute 阻止元素复用**

当组件需要进行重新渲染的时候，Vue 通过 diff 算法知道哪些元素可以复用以提高渲染效率。

但是有时候，我们需要切换一个全新的子元素，就比如我们前文介绍的 v-if 系列指令。

```html
<div id="app">
    <input v-if="flag" placeholder="input1">
    <input v-else placeholder="input2">
    <button @click="toggleFlag">Toggle</button>
</div>
<script>
    new Vue({
        el: '#app',
        data:{
            flag: true
        },
        methods:{
            toggleFlag(){
                this.flag=!this.flag;
            }
        }
    });
</script>
```

上面的结果是切换 input 时会保留原来的输入数据：

![动画](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151428680.gif)

如果你有阻止这样复用的需求，那么可以给元素添加一个唯一的 key 。

```html
<input v-if="flag" placeholder="input1" key="input1">
<input v-else placeholder="input2" key="input2">
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151428952.gif)

---

**v-show 的作用**

v-show 的用途和 v-if 指令一样，都是用于根据条件显示元素。

献上 v-show 源码

src\platforms\web\runtime\directives\show.ts

```typescript
import VNode from 'core/vdom/vnode'
import type { VNodeDirective, VNodeWithData } from 'types/vnode'
import { enter, leave } from 'web/runtime/modules/transition'

// recursively search for possible transition defined inside the component root
function locateNode(vnode: VNode | VNodeWithData): VNodeWithData {
  // @ts-expect-error
  return vnode.componentInstance && (!vnode.data || !vnode.data.transition)
    ? locateNode(vnode.componentInstance._vnode!)
    : vnode
}

export default {
  bind(el: any, { value }: VNodeDirective, vnode: VNodeWithData) {
    vnode = locateNode(vnode)
    const transition = vnode.data && vnode.data.transition
    const originalDisplay = (el.__vOriginalDisplay =
      el.style.display === 'none' ? '' : el.style.display)
    if (value && transition) {
      vnode.data.show = true
      enter(vnode, () => {
        el.style.display = originalDisplay
      })
    } else {
      el.style.display = value ? originalDisplay : 'none'
    }
  },

  update(el: any, { value, oldValue }: VNodeDirective, vnode: VNodeWithData) {
    /* istanbul ignore if */
    if (!value === !oldValue) return
    vnode = locateNode(vnode)
    const transition = vnode.data && vnode.data.transition
    if (transition) {
      vnode.data.show = true
      if (value) {
        enter(vnode, () => {
          el.style.display = el.__vOriginalDisplay
        })
      } else {
        leave(vnode, () => {
          el.style.display = 'none'
        })
      }
    } else {
      el.style.display = value ? el.__vOriginalDisplay : 'none'
    }
  },

  unbind(
    el: any,
    binding: VNodeDirective,
    vnode: VNodeWithData,
    oldVnode: VNodeWithData,
    isDestroy: boolean
  ) {
    if (!isDestroy) {
      el.style.display = el.__vOriginalDisplay
    }
  }
}
```

locateNode 函数递归寻找当前 vnode 外层具有 transition 属性的 vnode。

最后使用 export default 导出了一个[自定义指令](https://blog.csdn.net/2201_75288929/article/details/132307348?csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%22132307348%22%2C%22source%22%3A%222201_75288929%22%7D)的配置。

该自定义指令的bind函数的流程分析：

首先找寻外层具有 transtion 属性的 vnode，

```typescript
vnode = locateNode(vnode)
```

保存其 transition 属性值（如果不存在为false），

```typescript
const transition = vnode.data && vnode.data.transition
```

保存其原始的 display属性，如果本来 el.style.display 的值就为 none，那么 originalDisplay 为 空字符串`''`，

```typescript
const originalDisplay = (el.__vOriginalDisplay =
      el.style.display === 'none' ? '' : el.style.display)
```

判断指令的绑定表达式的值及是否是过渡组件，

如果绑定值为真值且是过渡组件，则调用 enter 函数进入过渡动画；

如果绑定值为假值，设置元素的 display 属性为 none；

如果绑定值是真值但不是过渡组件，设置元素的 display 属性为 originalDisplay，

```typescript
if (value && transition) {
  vnode.data.show = true
  enter(vnode, () => {
    el.style.display = originalDisplay
  })
} else {
  el.style.display = value ? originalDisplay : 'none'
}
```

所以，说这么多，**v-show 的本质上是通过 元素的 display attribute 来控制其显示**。

---

**v-show 和 v-if 的区别在哪里？**

v-show 本质上是切换元素的 CSS 属性 display 控制元素的可见性。

v-if 是直接创建或销毁DOM。

因此，**频繁切换元素使用 v-show，否则使用 v-if**。