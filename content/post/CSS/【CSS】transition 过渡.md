---
title: 【CSS】transition
description: 
slug: 
date: 2023-09-01
image: 
categories:
    - Frontend
tags:
    - CSS
---
# 1 前言

CSS过渡(transition)可以在一个元素切换到另一种状态时为其定义平滑的过渡效果。

例如，用户鼠标悬停在按钮上时，按钮颜色平滑的从一个颜色过渡到另一个颜色。

```css
.btn:hover{
    background-color: red;
    color: black;
}
```

默认悬停效果

![动画](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151540949.gif)

添加过渡效果

```css
.btn{
    transition-duration: 0.8s;
    transition-timing-function: ease;
}
```

![动画](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151540206.gif)

transition是**transition-property、transition-duration、transition-timing-function、transition-delay**的简写属性。

下面来一一介绍这些属性。

# 2 transition-property

这个属性指定应用过渡效果的属性名。

它有以下取值：

- `all`：默认值，表示过渡效果应用到所有可过渡的属性上。
- `none`：没有过渡动画。
- `<property-name>`：指定应用过渡效果的属性名，你可以指定多个值，使用逗号分隔。
- `initial`：重置为初始值。
- `unet`：重置为默认值。

# 3 transition-duration

过渡周期，过渡效果的持续时间。

默认值为0s，即没有过渡效果。

属性值以秒或毫秒为单位，像transition-property一样，你也可以设置多个值，它们会自动对应transition-property指定的属性名。

如果时间周期数小于过渡属性数，那么周期数会重复应用；如果时间周期数大于过渡属性数，那么时间周期数多余的部分被忽略。

# 4 transition-timing-function

过渡效果的时间函数，决定了动画变化速度。

![image-20230901204205527](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151540839.png)

下面是它的属性值介绍。

**1）ease**

默认值。

过渡在开始时速度较慢，中间时加速，结束时减速。

**2）ease-in**

过渡开始时速度较慢，然后逐渐加速。

**3）ease-out**

开始时速度较快，然后逐渐减速。

**4）ease-in-out**

过渡开始和结束时速度较慢，中间时速度较快。相比于ease更加平滑。

**5）linear**

过渡速度恒定，没有加速或减速，呈线性变化。

**6）step-start**

在过渡的开始时立即跳到结束状态。

**7）step-end**

在过渡的结束时立即跳回开始状态。

**8）steps函数**

例如steps(4,jump-end)表示将过渡划分为4步，每一步结束时立即跳到结束状态。

第二个参数有以下值：

- `jump-start`：在每一步开始时立即跳到结束状态。
- `jump-end`：在每一步结束时立即跳到结束状态。
- `jump-none`：没有跳跃，过渡效果平滑进行。
- `jump-both`：在每一步的开始和结束时都立即跳到结束状态。

**9）cubic-bezier函数**

![image-20230901204417462](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151541577.png)

接受四个参数，分别定义了时间曲线上的两个控制点。

这四个参数的取值范围是从0到1之间。

这四个参数的组合决定了贝塞尔曲线的形状，从而影响了过渡效果的速度和变化。

你可以使用[在线的贝塞尔曲线生成器](https://cubic-bezier.com/#.17,.67,.83,.67)来可视化和调整这些参数。

# 5 transition-delay

过渡之前需要等待的时间。

# 6 移动小球Demo

![动画](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151541213.gif)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>移动小球Demo</title>
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        .ball{
            position: absolute;
            width: 50px;
            height: 50px;
            border-radius: 50px;
            background-color: red;
            transition:all  7s ease;
        }
        .move {
            transform: translateX(300px); /* 将小球向右移动 */
        }
    </style>
</head>

<body>
    <button id="animateButton">移动</button>
    <div class="ball"></div>
    
    <script>
        const animateButton = document.getElementById("animateButton");
        const ball = document.querySelector(".ball");

        animateButton.addEventListener("click", () => {
            ball.classList.toggle("move");
        });
    </script>
</body>

</html>
```
