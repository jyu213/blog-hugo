+++
categories = ["translate"]
date = "2017-06-09T01:05:16+08:00"
description = ""
tags = ["translate", "css"]
title = "四个调节颜色的 CSS 滤镜"

+++

> 本文为译文，原文地址： http://vanseodesign.com/css/4-css-filters-for-adjusting-color/ ，作者，@Steven Bradley

SVG 提供了一种非破坏性的方式来改变图像或图形的某些颜色属性。不幸的是，其中的一些变化比其它的更麻烦。CSS 滤镜允许你无损的改变颜色的一些性质，比 SVG 简单。

最近几周我一直在谈论 CSS 滤镜作为 SVG 滤镜的替代品。首先我提供了一个[介绍](http://vanseodesign.com/css/css-filters-introduction/)和展示 `blur()` 滤镜函数的示例，然后我浏览了 [`url()` 和 `grop-shadow() `](镜函数，并分别提供了示例。
http://vanseodesign.com/css/drop-shadow-filter/) 滤镜函数，并分别提供了示例。

今天我想介绍四个 CSS 滤镜函数，所有这些都是 SVG 滤镜原语 `feColorMatrix` 的不同类型和值的映射。

## feColorMatrix 滤镜原语（Filter Primitive）
`feColorMatrix` 滤镜原语可以用于更改元素[颜色的一些基本属性](http://vanseodesign.com/web-design/hue-saturation-and-lightness/)。顾名思义，原语使用矩形来添加不同的滤镜效果。

存在四种不同的 CSS `filter-function` 来达到 `feColorMatrix` 的效果。这是一个例子，其中单个 SVG 原语可以比任何一个 CSS 滤镜功能做得更多。

这是四种 CSS 滤镜：

* grayscale()
* hue-rotate()
* saturate()
* sepia()

让我们在下图中做一些具体的实践。

![img1](http://www.vanseodesign.com/blog/wp-content/uploads/2013/09/strawberry-fields.jpg)

## grayscale() 函数
`grayscale()` `filter-function` 功能将图像转换为灰度。

```
grayscale() = grayscale( [ <number> | <percentage> ] )
```

你可以通过提供 0.0 到 1.0 之间的百分比或数字来确定转换图像的比例。100%（或 1.0）是完全转换为灰度，0% （或 0.0）则没有任何转换。0.0 和 1.0 或 0% 和 100% 直接的值是线性的乘数效应。负值是不被允许的。

在第一个例子中，我使用 `filter-function` 中的值 1 将 100% 的灰度应用到上图中。

```
.strawberry {
 filter: grayscale(1);
}
```

原始图像包含很多灰色，但我认为你可以看到滤镜的效果，因为现在所有的颜色都已被删除。

<img src="http://www.vanseodesign.com/blog/wp-content/uploads/2013/09/strawberry-fields.jpg" alt="img2" style="filter: grayscale(1);">

为了比较矩阵和 `filter-function`，公平起见，通过将 `type` 属性设置为 `saturate` 来使 `feColorMatrix` 更轻松地删除颜色。看如下部分代码：

```
<filter id="grayscale">
 <feColorMatrix type="matrix"
    values="(0.2126 + 0.7874 * [1 - amount]) (0.7152 - 0.7152 * [1 - amount]) (0.0722 - 0.0722 * [1 - amount]) 0 0
            (0.2126 - 0.2126 * [1 - amount]) (0.7152 + 0.2848 * [1 - amount]) (0.0722 - 0.0722 * [1 - amount]) 0 0
            (0.2126 - 0.2126 * [1 - amount]) (0.7152 - 0.7152 * [1 - amount]) (0.0722 + 0.9278 * [1 - amount]) 0 0 0 0 0 1 0"/>
</filter>
```

不过，这绝对是 CSS `filter-function` 功能更容易使用的情况。我知道使用这个特定的矩阵唯一的原因是我在网上发现一个例子，不需要搜索 `filter-function` 的值为 1。

## hue-rotate() 函数
`hue-rotate() filter-function` 将元素中每个像素的色调改变为你指定的数量。

```
hue-rotate() = hue-rotate( <angle> )
```

角度设置为度数，你需要将单位指定为 `deg`。 0deg 的角度使得元素不变， 和 360deg(720deg, 1080deg, 1440p等)的任意倍数一致。

这个例子中我设置了角度为 255deg。

```
.strawberry {
 filter: hue-rotate(225deg);
}
```

该值将红色和黄色的花朵变成含有更多粉色，紫色和蓝色的花朵。

<img src="http://www.vanseodesign.com/blog/wp-content/uploads/2013/09/strawberry-fields.jpg" alt="img3" style="filter: hue-rotate(225deg);">

以下是 SVG `filter` 的对比。CSS 依旧更简单。不过这种情况并不多。

```
<filter id="hue-rotate">
 <feColorMatrix type="hueRotate" values="225"/>
</filter>
```

## saturate() 函数
CSS 还提供了一个 `saturate() filter-function`，你可以使用它来更改元素饱和度。

```
saturate() = saturate( [ <number> | <percentage> ] )
```
`
和 `grayscale` 函数一样，该值定义了转让的比例。0% （或 0.0） 导致完全饱和度的元素，100%（或 1.0） 使元素保持不变。之间的值是线性的乘数效应。

这里我设置了 50%。

```
.strawberry {
 filter: saturate(0.5);
}
```

返回图片如下。

<img src="http://www.vanseodesign.com/blog/wp-content/uploads/2013/09/strawberry-fields.jpg" alt="img4" style="filter: saturate(0.5);">

不允许使用负值，但是你可以提供大于 100% 或 1.0 的值。使得元素超饱和、再次使用 900% 饱和度的图像（ `filter:saturate(9);`）。

<img src="http://www.vanseodesign.com/blog/wp-content/uploads/2013/09/strawberry-fields.jpg" alt="img5" style="filter: saturate(9);">

如同 `saturate()`，相应的 SVG `filter` 是比较简单的。

```
<filter id="saturate">
 <feColorMatrix type="saturate" values="0.5"/>
</filter>
```

我之前提到，你可以通过将 `type` 属性设置为 `saturate`，来使 `feColorMatrix` 更轻松地创建灰度图像。你只需要将值设置为 0，使得图像完全饱和，这与将其设置为 100% 灰度产生。

## sepia() 函数
最后还有 `sepia() filter-function` 将图像转换为深褐色。

```
sepia() = sepia( [ <number> | <percentage> ] )
```

现在应该很熟悉，但是值定义了转换的比例。100%（1.0）是完全深褐色，而 0%（0.0）则保持图像不变。期间的值是效果间的线性乘数效应。

不允许使用负值，但是你可以提供大于 100% 或 1.0 的值，但不会添加效果。

这里我设置为 75%。

```
.strawberry {
 filter: sepia(75%);
}
```

图片如下：

<img src="http://www.vanseodesign.com/blog/wp-content/uploads/2013/09/strawberry-fields.jpg" alt="img6" style="filter: sepia(75%);">

对于没有 `sepia`  类型的 `feColorMatrix`，所有想要获得与使用另一个矩阵的深褐色效果。

```
<filter id="sepia">
 <feColorMatrix type="matrix"
    values="(0.393 + 0.607 * [1 - amount]) (0.769 - 0.769 * [1 - amount]) (0.189 - 0.189 * [1 - amount]) 0 0
            (0.349 - 0.349 * [1 - amount]) (0.686 + 0.314 * [1 - amount]) (0.168 - 0.168 * [1 - amount]) 0 0
            (0.272 - 0.272 * [1 - amount]) (0.534 - 0.534 * [1 - amount]) (0.131 + 0.869 * [1 - amount]) 0 0 0 0 0 1 0"/>
</filter>
```

我认为你同意 CSS 滤镜功能是这两个选项，即使 SVG 在更灵活配置。

## 总结
这四个 CSS 过滤器功能都是 滤镜原语 `feColorMatrix` 的不同类型和值的映射。其中两个代替复杂的矩阵，而另外两个替换一个特定类型的原语。

我希望您同意，所有这四个过滤功能都足够容易理解和使用。我怀疑你会很难与他们合作，或弄清楚用来调整图像和图形的值。

下一周，我将通过查看四个更多的[滤镜原语](http://vanseodesign.com/web-design/svg-filter-primitives-fecomponenttransfer/)创建的效果。与今天四个功能一样，我相你会下周的功能通常与原语更若以使用。
