---
title: 【CSS】水平垂直居中方案
description: 
slug: 
date: 2023-09-02
image: 
categories:
    - Frontend
tags:
    - CSS
---
# 1 前言

水平居中、垂直居中是前端面试百问不厌的问题。

其实现方案也是多种多样，常叫人头昏眼花。

> **水平方向可以认为是内联方向，垂直方向认为是块级方向。**

下面介绍一些常见的方法。

```html
<div class="container">
    <span class="innerText">Hello,World!</span>
</div>
```

![image-20230904193415921](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202309041934036.png)

# 2 内联元素的水平垂直居中

首先，常见内联元素有：**a、span、em、b、strong、i、button**。

## 2.1 使用弹性布局

使用`dispaly: flex`将父级容器设置为[弹性布局](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Flexbox)（Flexbox），然后可以通过`justify-content: center`控制水平居中，使用`align-items: center`控制垂直居中。

```css
.container {
    height: 100px;
    width: 200px;
    background-color: cadetblue;
    display: flex;
    /* 水平居中 */
    justify-content: center;
    /* 垂直居中 */
    align-items: center;
}
```

## 2.2 使用网格布局

使用`dispaly: grid`将父级容器设置为**网格布局**（Grid），然后可以通过`place-items: center;`控制水平垂直居中。

```css
.container {
    height: 100px;
    width: 200px;
    background-color: antiquewhite;
    display: grid;
    place-items: center;
}
```

`place-items`是`align-items`、`justify-items`的简写。

## 2.3 使用text-align和line-hight

设置父级容器的`text-align: center`实现文本水平居中对齐，关键的一步**设置行高`line-height`为容器的高度即可实现文本垂直居中**。

> 为什么设置行高`line-height`为容器的高度就能实现文本垂直居中？
>
> 因为文本的基线位于行高的中间，基线也是文本的中线，设置行高等于高度后，文字就垂直居中了。

```css
.container {
    height: 100px;
    width: 200px;
    background-color: antiquewhite;
    /* 水平居中 */
    text-align: center;
    /* 垂直居中，行高等于高度 */
    line-height: 100px;
}
```

## 2.4 使用text-align和display: table-cell

将元素设置为表格单元格，再使用`vertical-align: center`实现垂直居中。

```css
.container {
    height: 100px;
    width: 200px;
    background-color: antiquewhite;
    display: table-cell;
    /* 水平居中 */
    text-align: center;
    /* 垂直居中 */
    vertical-align: middle;
}
```

# 3 块级元素的水平垂直居中

常见块级元素有：**h1-h6、p、div、ul、ol、li**等。

```html
<div class="container">
    <div class="innerText"></div>
</div>
```

**前面介绍的内联元素的水平垂直居中方法也适用于块级元素**。下面就不再重复介绍。

## 3.1 绝对定位+margin: auto

首先，设置父元素为相对定位。

为什么要设置父元素为相对定位？

1. 创建一个提供给子元素的定位坐标系，使得子元素在该坐标系内定位。
2. 绝对定位的元素会脱离正常文档流，并相对于最近的已定位组件进行定位。
3. `margin: auto`计算居中位置依赖于外部的相对定位的父元素。

设置子元素为绝对定位，其top、left、right、bottom的值设为0，margin为auto即可让子元素自动调整四周距离一样实现水平垂直居中。

```css
.container {
    height: 100px;
    width: 200px;
    background-color: cadetblue;
    position: relative;
}
.innerText {
    background-color: black;
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    width: 50px;
    height: 50px;
    /* 水平垂直居中 */
    margin: auto;
}
```

## 3.2 绝对定位+负margin

除了自动计算，我们还可以根据长宽手动指定元素移动距离。

使用负margin值实现元素的平移。

```css
.container {
    height: 100px;
    width: 200px;
    background-color: cadetblue;
    position: relative;
}
.innerText {
    width: 50px;
    height: 50px;
    background-color: black;
    position: absolute;
    top: 50%;
    left: 50%;
    margin: -25px 0 0 -25px;
}
```

## 3.3 绝对定位+transform

使用transform实现元素的平移。

```css
.container {
    height: 100px;
    width: 200px;
    background-color: cadetblue;
    position: relative;
}
.innerText {
    width: 50px;
    height: 50px;
    background-color: black;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
}
```