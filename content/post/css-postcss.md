+++
date = "2016-02-17T00:30:56+08:00"
draft = true
title = "CSS漫谈 - PostCSS"
description = ""
tags = ["css", "PostCSS"]
categories = ["css"]
+++

### 写在前面
第一个听到 PostCSS 这个名词，还是在 15 年中旬的时候（同事做的一次分享中）。当时的感觉就一个字，酷。不过由于种种因素，一直没有好好的去跑在实际项目中。

### CSS 大环境
这里应该准备另外一篇来介绍的。暂无。从 Sass/Less 面世之后，预处理器的概念也已经被越来越多的人所接受的。的确，传统的 CSS 方式啥都没有，编程模式太弱了。随着前端行业不断的快速迭代，也越来越无力维护了。

目前比较常见的工作流是这样的：

Sass/Less -> css(dev) -> css(production)

从预处理器开始，编译执行后再转化为生成环境的 CSS 内容。而在第二步中，产生了一个新名词“后处理器”。

...

PostCSS 是一个后处理器的框架，可以用于分析 CSS 规则，并给出 AST（抽象语法树），可以很简单的使用 JS 去修改 CSS 内容。这样，一些添加前缀，浏览器 hack，等等体力活就可以交给 postcss 来处理了。认真的讲的话，postcss 经过组件的集合甚至可以达到预处理器的能力。总而言之，这是一个自动化的用于提高工作效率的工具，更使得 CSS 模块化有进一步的空间。

### 原理
其实 PostCSS 自身只包含了 CSS 分析器，CSS 节点树，source map 生成树和 CSS 节点树拼接。原理很简单，先读取 CSS 字符内容，得到一个完整的节点树，然后对该节点树进行相关操作，该操作一般引用外部插件处理。最后对新的节点树重组为 CSS 字符。期间可生成 source map。

### 使用
PostCSS 可以结合主流打包工具（Grunt, gulp, webpack 等等），操作比较简单，大家可以移步[官网](https://github.com/postcss/postcss)查看具体的操作。

也可以看[w3cplus的简易教程](http://www.w3cplus.com/blog/tags/516.html)

### 感想
* PostCSS 的插件引入需要注意顺序
* 整体使用简单。但是如果想要达到或者接近预处理器的功能的话，可能需要衡量一下了。毕竟使用不同的第三方插件可能会导致一些语法糖不一致，反而加大的学习成本。

### 扩展阅读：
* [PostCSS Deep Dive](http://webdesign.tutsplus.com/tutorials/postcss-deep-dive-what-you-need-to-know--cms-24535)
