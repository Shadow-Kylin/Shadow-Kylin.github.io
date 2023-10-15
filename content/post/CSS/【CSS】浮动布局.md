---
title: 【CSS】浮动布局
description: 
slug: 
date: 2023-09-15
image: 
categories:
    - Frontend
tags:
    - CSS
---
# 浮动的设计初衷

```
float: left/right/both;
```

浮动是网页布局最古老的方式。

浮动一开始并不是为了网页布局而设计，它的初衷是**将一个元素拉到一侧，这样文档流就能够包围它**。

常见的用途是文本环绕图片：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151541119.png)

**浮动元素会被移出正常文档流，并被拉到容器边缘**。

# 清除浮动的原因及方法

**浮动元素的高度不会追加到父元素上**。

如果浮动的元素比容器高，那么就可能发生**容器折叠**现象：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151541750.png)

这时，我们就需要清除浮动。

清除浮动不太优雅的方式：在容器末尾添加一个空 div，设置 `clear: both`，清除两边浮动，使得容器会向下扩展包含它。

```html
<div style="clear: both;"></div>
```

既然是添加一个 div 元素，不如使用伪元素 `::after` 来实现。

```css
.clearfix::after{
	display: block;
	content: " ";
	clear: both;
}
```

**这个 `clearfix` 类是添加到包含浮动元素的元素上，而不是浮动元素本身。**

使用 `display: block` 的原因：默认情况下，伪元素是内联元素，而不是块级元素。为了确保伪元素占据一整行并且在浮动元素之后换行，我们需要将其设置为块级元素。更重要的是，`clear` 属性只对块级元素生效。

设置 `content: " "` 的原因：解决一些旧版浏览器的 Bug。

---

清除浮动后的另一个问题：**浮动元素的外边距不会折叠到清除浮动后的容器外部，但是非浮动元素会**。

对此，解决该问题的`clearfix`的修改版如下：

```css
.clearfix::after,
.clearfix::before{
    display: table;
    content: " ";
}

.clearfix::after{
    clear: both;
}
```

为什么使用 `display:table` 而不是 `display: block` ：外边距无法通过单元格元素折叠。

---

浮动陷阱：**浏览器会将浮动元素尽可能地放在靠上的地方**。

如果众多的浮动元素高度不一致，最后导致布局会千变万化。哪怕是 `1px` 的高度差距也会导致浮动陷阱。

解决方法：**给每一行的第一个元素清除左浮动**。

假设每行有 m 个元素：

```css
.floatElement::nth-child(mn+1){
	clear: left
}
```

# 浮动布局示例解析：古诗欣赏

初始源代码如下：

index.html

```html
<div class="container">
	<header>
		<h1>古诗欣赏</h1>
	</header>
	<main class="main clearfix">
		<h3>五言绝句</h3>
		<div>
			<div class="media">
				<img class="media-image" src="相思.png">
				<div class="media-body">
					<h4>相思·王维</h4>
					<p>
						红豆生南国，春来发几枝。
						愿君多采撷，此物最相思。
					</p>
				</div>
			</div>
			<div class="media">
				<img class="media-image" src="听筝.png">
				<div class="media-body">
					<h4>听筝·李端</h4>
					<p>
						鸣筝金粟柱，素手玉房前。
						欲得周郎顾，时时误拂弦。
					</p>
				</div>
			</div>
			<div class="media">
				<img class="media-image" src="江雪.png">
				<div class="media-body">
					<h4>江雪·柳宗元</h4>
					<p>
						千山鸟飞绝，万径人踪灭。
						孤舟蓑笠翁，独钓寒江雪。
					</p>
				</div>
			</div>
			<div class="media">
				<img class="media-image" src="春晓.png">
				<div class="media-body">
					<h4>春晓·孟浩然</h4>
					<p>
						春眠不觉晓，处处闻啼鸟。
						夜来风雨声，花落知多少。
					</p>
				</div>
			</div>
		</div>
	</main>
</div>
```

style.css

```css
:root {
    box-sizing: border-box; /* 修改盒模型 */
}

*,
::before,
::after {
    box-sizing: inherit; /* 继承 box-sizing */
}

body {
    background-color: aliceblue;
    font-family: Arial, Helvetica, sans-serif;
}

/* 猫头鹰选择器 */

body * + *{
    margin-top: 1.5em;
}

header{
    padding: 1em 2em;
    background-color: antiquewhite;
    border-radius: .5em;
    margin-bottom: 2em;
}

.main{
    padding: 0 1.5em;
    background-color: white;
    border-radius: .5em;
}

.container{
    max-width: 800px; /* 自动适配宽度 */
    margin: 0 auto; /* 双容器模式 水平居中 */
}

.media{
    float: left;
    margin: 0 1.5em 1.5em 0; /* 重置 margin */
    width: calc(50% - 1.5em); /* 从宽度里减去 1.5em */
    padding: 1.5em;
    background-color:rgb(238, 245, 247);
    border-radius: .5em;
}

.media-image{
    width: 60px;
    height: 60px;
}

/* 清除浮动 */

/* .clearfix::after{
	display: block;
	content: " ";
	clear: both;
} */

/* 清除浮动修改版 */

.clearfix::after,
.clearfix::before{
    display: table;
    content: " ";
}

.clearfix::after{
    clear: both;
}

/* 解决浮动陷阱 */

.media:nth-child(odd){
    clear: left;
}
```

效果图：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151541161.png)

## 实现图片被文字环绕

```css
.media-image{
    width: 100px;
    height: 100px;
    float: left; /* 左浮动 */
}

.media-body{
    margin-top: 0; /* 覆盖猫头鹰选择器 */
}

.media-body h4{
    margin-top: 0; /* 覆盖用户代理样式表 */
}
```

效果：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151551856.png)

## 实现图片在左文字在右

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151541951.png)

如上，图片被包含在了相邻的同级元素 `media-body` 中。

如果想实现图片在左文字在右，可以为文字创建一个**块级格式上下文**（block formatting context，BFC）。

BFC 将内部与外部隔绝开，内外互不影响。

创建 BFC 的方式：

1. float 不为 none。
2. overflow 不为 visible。
3. display 为 inline-block、table-cell、table-caption、flex、inline-flex、grid、inline-grid。
4. position 为 absolute 或 fixed。

网页的根元素就是一个顶级的 BFC。

CSS 修改如下：

```css
.media-image{
    width: 60px;
    height: 60px;
    float: left;
    margin-right: 1.5em; /* 图片与文字间增加一定间距 */
}

.media-body{
    margin-top: 0; /* 覆盖猫头鹰选择器 */
    overflow: auto; /* 创建 BFC */
}
```

效果图：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151541992.png)

注意：使用浮动或 `display: inline-block`创建BFC的元素的宽度会变为 `100%` 。

# 基于浮动实现网格系统

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151541610.png)

大部分的 CSS 框架都实现了自己的网格系统：**在一个行容器内放置若干列容器，列的宽度由列容器的类决定**。

```html
<div class="row">
	<div class="column-1"></div>
	<div class="column-1"></div>
	<div class="column-1"></div>
	<div class="column-1"></div>
	<div class="column-1"></div>
	<div class="column-1"></div>
	<div class="column-3"></div>
	<div class="column-3"></div>
</div>
```

使用网格系统可以提高代码的可复用性。

网格系统不参与行列元素的视觉样式，只负责设置宽度和定位。在行列内的元素就不必再考虑宽度和定位了。

```css
/* 网格系统 */

.row::after{
    display: block;
    content: " ";
    clear: both;
}

[class*="column-"]{
    float: left;
    padding: 0 0.75em; /* 左右设置内边距 */
    margin-top: 0; /* 去掉顶部外边距 */
}

.column-1{
    width: 8.333%;
}
.column-2{
    width: 16.6667%;
}
.column-3{
    width: 25%;
}
.column-4{
    width: 33.3333%;
}
.column-5{
    width: 41.6667%;
}
.column-6{
    width: 50%;
}
.column-7{
    width: 58.333%;
}
.column-8{
    width: 66.6667%;
}
.column-9{
    width: 75%;
}
.column-10{
    width: 83.333%;
}
.column-11{
    width: 91.6667%;
}
.column-12{
    width:100%;
}
```

完整 CSS：

```css
:root {
    box-sizing: border-box; /* 修改盒模型 */
}

*,
::before,
::after {
    box-sizing: inherit; /* 继承 box-sizing */
}

body {
    background-color: aliceblue;
    font-family: Arial, Helvetica, sans-serif;
}

/* 猫头鹰选择器 */

body * + *{
    margin-top: 1.5em;
}

header{
    padding: 1em 2em;
    background-color: antiquewhite;
    border-radius: .5em;
    margin-bottom: 2em;
}

.main{
    padding: 0 1.5em 1.5em;
    background-color: white;
    border-radius: .5em;
}

.container{
    max-width: 800px; /* 自动适配宽度 */
    margin: 0 auto; /* 双容器模式 水平居中 */
}

/* 媒体对象的样式 */

.media{
    padding: 1.5em;
    background-color:rgb(238, 245, 247);
    border-radius: .5em;
}

.media-image{
    width: 60px;
    height: 60px;
    float: left;
    margin-right: 1.5em;
}

.media-body{
    margin-top: 0; /* 覆盖猫头鹰选择器 */
    overflow: auto; /* 创建 BFC */
}

.media-body h4{
    margin-top: 0; /* 覆盖用户代理样式表 */
}

/* 清除浮动 */

/* .clearfix::after{
	display: block;
	content: " ";
	clear: both;
} */

/* 清除浮动修改版 */

.clearfix::after,
.clearfix::before{
    display: table;
    content: " ";
}

.clearfix::after{
    clear: both;
}

/* 网格系统 */

.row::after{
    display: block;
    content: " ";
    clear: both;
}

[class*="column-"]{
    float: left;
    padding: 0 0.75em; /* 左右设置内边距 */
    margin-top: 0; /* 去掉顶部外边距 */
}

.column-1{
    width: 8.333%;
}
.column-2{
    width: 16.6667%;
}
.column-3{
    width: 25%;
}
.column-4{
    width: 33.3333%;
}
.column-5{
    width: 41.6667%;
}
.column-6{
    width: 50%;
}
.column-7{
    width: 58.333%;
}
.column-8{
    width: 66.6667%;
}
.column-9{
    width: 75%;
}
.column-10{
    width: 83.333%;
}
.column-11{
    width: 91.6667%;
}
.column-12{
    width:100%;
}
```

效果图如下：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151542393.png)

与前面的相比，这个导致了内容出现了错位，没有对齐标题。

使用负 margin 拉伸行元素解决该问题：

```css
/* 网格系统 */

.row{
    margin-left: -0.75em;
    margin-right: -0.75em;
}

...
...
```

效果图：

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151542725.png)

[最终代码](https://download.csdn.net/download/2201_75288929/88346941?spm=1001.2014.3001.5503)。