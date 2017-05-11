+++
categories = ["translate", "css"]
date = "2017-05-10T22:34:58+08:00"
description = ""
tags = ["translate", "css"]
title = "【译】CSS 网格的春天"

+++

> 本文为译文，原文地址： http://jonibologna.com/spring-into-css-grid，作者，@jonitrythall

CSS 网格(Grid)最近一直受到很多的关注，我终于在过去的一个周末腾出时间来了解它的基本工作原理。道路很曲折，前途很美好（说真的，这是改变生活的东西），但是我在创建示例并分享的同时，整理了一些笔记。

这篇文章不是只看看这个布局功能有多强大，而是希望它在这混乱中把威胁因素移除，或者进入 CSS 网格。

[点击这里查看完整的 Demo](http://codepen.io/jonitrythall/full/xdOrrq/)

### 制定一个计划
和学习任何网络新事物一样，我立即打开 `Adobe Illustrator`，感受自然，网格和紫色带来的灵感，我开始绘制一个基本的户外场景，然后开始尝试用 CSS 网格来实现。

下图为我的最终搞：

![images](http://jonibologna.com/content/images/2017/04/scene-01.png)

### 标记
标记由一个 `class` 名为 `contain` 的 `div` 主容器以及十二个 `class` 名为 `spring` 的子集 `div` 组成。HTML 和 CSS 代码片段如下：

```html
<div class="contain">
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/sun.svg" alt="Sun" width="15%" />
  </div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/cloud_copy.svg" alt="Cloud" width="30%" />
  </div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/cloud_copy.svg" alt="Cloud" width="30%" />
  </div>
  <div class="spring"><img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/rain.svg" alt="Rain" width="60%" /></div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/rain.svg" alt="Rain" width="60%" />
  </div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/rain.svg" alt="Rain" width="27%" />
  </div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/dragonfly.svg" alt="Dragonfly" width="30%" />
  </div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/butterfly.svg" alt="Butterfly" width="30%" />
  </div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/dragonfly.svg" alt="Dragonfly" width="30%" />
  </div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/butterfly.svg" alt="Butterfly" width="30%"/>
  </div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/flower1.svg" alt="First flower" />
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/flower2.svg" alt="Second flower" />
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/flower3.svg" alt="Third flower" />
  </div>
  <div class="spring">
    <img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/80625/flowers2.svg" alt="Fourth flower" />
  </div>
</div>
```

```sass
$orange: #FB9C6F;

body {
  border: 2px solid $orange;
}

.contain {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(3, 2fr) 1fr 4fr;
  grid-gap: 2px;
  background-color: $orange;
}

.spring {
  padding: 2em;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #FFF;
}

// Sun
.spring:nth-child(1) {
  grid-column: 1 / 5;
}

// Cloud 1
.spring:nth-child(2) {
  grid-column: 1 / 3;
}

// Cloud 2
.spring:nth-child(3) {
  grid-column: 3 / 5;
}

// Rain 1
.spring:nth-child(4) {
  grid-column: 1 / 2;
}

// Rain 2
.spring:nth-child(5) {
  grid-column: 2 / 3;
}

// Rain 3
.spring:nth-child(6) {
  grid-column: 3 / 5;
}

// Flowers 1
.spring:nth-child(11) {
  grid-column: 1 / 4;
  justify-content: space-around;
}

// Flowers 2
.spring:nth-child(12) {
  grid-column: 4 / 5;
}

// Just simplifying for smaller window
@media (max-width: 550px) {
  .contain {
    display: block;
  }

  .spring:nth-child(3),
  .spring:nth-child(5),
  .spring:nth-child(6),
  .spring:nth-child(7),
  .spring:nth-child(8),
  .spring:nth-child(11) {
    display: none;
  }

  img {
    width: 30%;
  }
}
```

在实现网格之前，布局是标准的状态，所有的图像堆叠在一起。如下图：

![images](http://jonibologna.com/content/images/2017/04/Screen-Shot-2017-04-23-at-12-27-03-PM.png)

### columns
使用网格时首先需要在 CSS 网格容器中声明它：

```css
.contain {
    display: grid;
}
```

> 注意：这里也有 `inline-grid` 和 `subgrid` 作为选项。

这里将立即设置容器内所有的子元素为网格项。

初始网格基于使用 `grid-template-columns` 属性设置的四个相同大小的列。

```css
.contain {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
}
```

`repeat` 函数允许缩短 `1fr 1fr 1fr 1fr` 的写法。

### `fr` 单位
网格属性可以接受多个不同的单位值，本例中只使用了 `fr`。这个单位是超级聪明和灵活的，因为它将可用空间划分为分数，所以 `1fr 1fr 1fr 1fr` 将生成四个相同大小的列。

### 网格间距
网格间距由每个项目的 `2px` 橙色间距表示。间隙显示主容器的背景颜色，为橙色，外边框是 `body` 本身。每个网格包含一个白色背景的图像。

![images](http://jonibologna.com/content/images/2017/04/Screen-Shot-2017-04-23-at-12-01-19-PM.png)

示例使用缩写 `grid-gap` 来匹配 `grid-column-gap` 和 `grid-row-gap`。该属性接受单个共享值或者两个不同的值，第一个参数表示行，第二个表示列。

### 从列起点和终点来排列项目
一旦在主容器上设置基本列，容器中的每个子元素 `div` 都可以单独进一步设计，已实现以下网格：

![images](http://jonibologna.com/content/images/2017/04/Screen-Shot-2017-04-23-at-1-02-31-PM.png)

这是通过 `grid-column-start` 和 `grid-column-end` 属性或 `grid-column` 简写来指定元素的起点和终点来完成的。

例如，每个后代元素 `div` 起点和终点定义如下：

```css
.spring:nth-child(4) {
  grid-column: 1 / 2;
}

.spring:nth-child(5) {
  grid-column: 2 / 3;
}

.spring:nth-child(6) {
  grid-column: 3 / 5;
}
```

第四列项目（第一个雨图像容器）的 `grid-column: 1 / 2 ;` 指示它在第一垂直网格线开始并在第二列结束，而第六项（第三个云图像容器）被设置为在第三垂直网格线开始并在第五列结束，使其成为其它的两倍。

![images](http://jonibologna.com/content/images/2017/04/Screen-Shot-2017-04-23-at-2-30-25-PM.png)

最初这可能有点棘手，因为垂直线超过了实际网格列单元的个数。

太阳图像能够通过从第一垂直网线开始并在最后一行终止，即第五行，跨越所有四个可用单元格，从而占据全宽。

```css
.spring:nth-child(1) {
  grid-column: 1 / 5;
}
```

![images](http://jonibologna.com/content/images/2017/04/Screen-Shot-2017-04-24-at-8-50-24-AM.png)

没有必要对蜻蜓图像和蝴蝶图像做任何事情，因为所需的布局与主容器 `.contain` `div` 上的列声明一致。

![images](http://jonibologna.com/content/images/2017/04/Screen-Shot-2017-04-23-at-12-13-05-PM.png)

### rows
回到容器元素设置行，这是我来演示这个奇怪风景的最后一步。尽管初始列由相等的间距组成，但是行具有更多的变化。

![images](http://jonibologna.com/content/images/2017/04/Screen-Shot-2017-04-23-at-12-57-19-PM.png)

前三行是最小行的两倍，而最大行是最小行的四倍，如下所示： `grid-template-rows: repeat(3, 2fr) 1fr 4fr;`

这个设计对于行的需求是相当轻的，这就是行样式所需的程度，但是行有自己的一组属性，有些甚至可以与列属性组合来创建速记法，保持 CSS 的简洁。

### Flexbox
谈及 flexbox（并且最近创建了一个[印刷视觉指南](https://gum.co/YdWw)），我只想说，在这个示例中，`flexbox` 用于将图像在网格中居中，而网格和 `flexbox` 实际上可以一起使用。

使用如下，使得每张图片都在项目中居中：

```css
.spring {
    display: flex;
    justify-content: center;
    align-items: center;
}
```

`div` 包含三个独立的花图像，`flexbox` 也水平划分它们。

![images](http://jonibologna.com/content/images/2017/04/Screen-Shot-2017-04-23-at-12-15-43-PM.png)

```css
.spring:nth-child(11) {
    display: flex;
    justify-content: space-around;
}
```

`Robin Rendle` 最近在 `CSS-Tricks` 上写了有关这个主题的一个[很好的总结](https://css-tricks.com/css-grid-replace-flexbox/)。

### 浏览器支持
目前浏览器支持状况：

![images](http://jonibologna.com/content/images/2017/04/Screen-Shot-2017-04-23-at-1-24-17-PM.png)

### 资源
这里有一些比较好的扩展阅读：

* [Getting Started with CSS Grid](https://css-tricks.com/getting-started-css-grid/)
[Basic CSS Grid Concepts on MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout)
[CSS Grid Layout on MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout)
[The spec](https://www.w3.org/TR/css3-grid-layout/)
[CSS Grid Garden](http://cssgridgarden.com/)

### 结尾
要了解 CSS Grid 的功能，还需要学习更多的东西，我迫不及待地写更多的内容，但希望这篇文章可以作为一个有趣的起点。
