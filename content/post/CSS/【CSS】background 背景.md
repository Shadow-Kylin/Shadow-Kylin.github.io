---
title: 【CSS】background
description: 
slug: 
date: 2023-08-24
image: 
math: true
categories:
    - Frontend
tags:
    - CSS
---
`background`属性为元素添加背景效果。

它是以下属性的简写，按顺序为：

1. **background-color**
2. **background-image**
3. **background-repeat**
4. **background-position**
5. **background-size**
6. **background-origin**
7. **background-clip**
8. **background-attachment**

```css
.element {
  background: #ff0000 url('background.jpg') no-repeat top right / 200px 150px content-box border-box fixed;
}

```

以下所有示例中的`花花.jpg`图片的大小是**48×48**。

---

# 1 background-color

`background-color`指定元素的背景色。

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS 背景色 Demo</title>
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        .container {
            width: 200px;
            height: 200px;
            background-color: #f0f0f0;
        }
        .box {
            width: 100px;
            height: 100px;
            background-color: #f00;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="box"></div>
    </div>
</body>
</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539852.png)

---

# 2 background-image

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS 背景图片 Demo</title>
    <style>
        body {
            margin: 0;
            padding: 0;
        }

        .bg {
            width: 96px;
            height: 96px;
            background-image: url('./花花.jpg')
        }
    </style>
</head>

<body>
    <div class="bg"></div>
</body>

</html>
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539371.png)

背景图片默认是重复的（repeat）。

---

# 3 background-repeat

它具有以下值。

**1）默认值repeat**

repeat会裁剪重复图片超出的部分。

```css
.bg{
    width: 120px;
    height: 96px;
    background-repeat:repeat;
}
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539581.png)

**2）space**

space是无裁剪的重复。

其重复原理：

- 如果图片不能刚好放下，剩余长或宽将均匀分布在图片之间。
- 第一张图片一定从左上方的顶点位置开始排列。
- 如果图片大小超出容器大小，那么将被裁剪。

```css
.bg{
    width: 120px;
    height: 96px;
    background-repeat:space;
}
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539732.png)

```css
.bg{
    width: 160px;
    height: 96px;
    background-repeat:space;
}
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539530.png)


**3）round**

round是自适应重复，相比较于space，它会根据元素与图片的大小关系拉伸或缩小图片。

比如说，一个图片长为$x$，元素长为$X$，$nx \leq X \leq mx$，如果$X$更靠近$nx$，那么图片将被放大，如果$X$更靠近$mx$，那么图片将被缩小。

**4）no-repeat**

`no-repeat`设置图片不允许重复。

---



# 4 background-position

`background-position`用于设定图片的位置，其值类型如下：

```css
.bg{
    /*关键字*/
    background-position:top;
    background-position:bottom;
    background-position:center;
    background-position:left;
    background-position:right;
    
    /*百分比值*/
    background-position:50% 50%;
    
    /*长度值*/
    background-position:10cm 10cm;
    
    /*边界偏移值*/
    background-position:top 10em left 10px;
    
    /*全局值*/
    background-position:inherit; /*继承父元素*/
    background-position:initial; /*设置为初始值，默认*/
    background-position:revert; /*重置为样式表中的值*/
    background-position:unset; /*重置为初始值，如果父元素背景位置有定义，继承父元素的值*/
}
```

# 5 background-size

用于控制背景图片的尺寸和适应方式。

初始值：auto auto。

以下是一些常见的 `background-size` 值和解释：

示例使用背景图片（*390x693* ）：

![lg_138312_1623941180_60cb603c0df23](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539970.jpeg)

**1）auto**

默认值，背景图片大小保持原始尺寸，不进行缩放。

**2）长度值**

使用指定的长度单位（如像素）设置背景图片的宽度和高度。

例如，`background-size: 200px 100px;` 将背景图片的宽度设置为 200 像素，高度设置为 100 像素。

如果只设置一个值，那么该值将作为宽度，高度设为auto。

**3）百分比**

使用指定的百分比值设置背景图片的宽度和高度。

**百分比是相对于包含元素的宽度和高度计算的**。

例如，`background-size: 50% 75%;` 将背景图片的宽度设置为元素宽度的 50%，高度设置为元素高度的 75%。

**4）cover**

背景图片会**完全覆盖整个元素**，可能会裁剪部分图片。

背景图片会被**等比例缩放，**以适应元素的较小维度，然后将其余部分裁剪掉，以确保整个元素都被覆盖。

较小维度是看 *元素长/图片长* 以及 *元素宽/图片宽* 的比值大小。

```css
width: 390px;
height: 500px;
background-size:cover
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151605742.png)


**5）contain**

确保整个背景图片完全包含在元素内，不会裁剪图片。

背景图片会被等比例缩放，以适应元素的较大维度，以确保整个图片都可见，不会被裁剪。

```css
width: 390px;
height: 500px;
background-size:contain
```

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539373.png)


# 6 background-origin

指定背景图片的原点位置在哪个区域的左上角。

该属性有以下值：

1）`padding-box`：默认值。

```css
.bg {
    width: 48px;
    height: 48px;
    background-image: url('./花花.png'); 
    background-origin: padding-box;
    padding: 5px;
    border: 5px solid #b95353;
}
```

![image-20230901113621481](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539565.png)

2）`border-box`。

![image-20230901113845995](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539197.png)

3）`content-box`。

![image-20230901113929660](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539850.png)

# 7 background-clip

`background-clip` 设置元素的背景（背景图片或颜色）是否延伸到边框、内边距盒子、内容盒子下面。

![动画](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151539076.gif)

# 8 background-attachment

`background-attachment`决定图片的滚动行为。

其值包括：

`scroll`（默认值）：背景图片随页面滚动而滚动。

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151540810.gif)

`fixed`：背景图片不会随页面滚动而滚动，而是相对于视口的原点（左上角）固定。

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151540852.gif)

我们观察到这两个元素的背景图片是重叠在一起了。

`local`：不随页面滚动，但随元素内部滚动而滚动。

![在这里插入图片描述](https://raw.githubusercontent.com/Shadow-Kylin/BlogImages/main/202310151540275.gif)