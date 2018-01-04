+++
title = "CSS 模块化组织之道"
date = "2017-04-01"
categories = ["css"]
tags = ["css"]
+++

> 前几天还在问朋友，CSS 近期有没有推出啥比较火热的，答，除了 `Grid` 好像没啥新东西。CSS 确实好像没有 JS 社区那么火热了。

由于 CSS 自身语言的特点，操作起来非常容易，但是等到项目到达一定程度的时候，想要优雅的管理 CSS，却也不见得那么容易。

大家平常在开发 CSS 的过程中， 肯定遇到过下面的一些情况：

* 全局命名空间容易冲突
* 文件依赖管理的混乱
* 层级嵌套太深
* 样式被重置
* 大量重复、冗余、无用的代码

好的 CSS 设计应该具有**可读性**，**维护性**，**扩展性**和**复用性**。这几条其实也不用特地的去解释，在软件开发设计中并不陌生。大体来说，就是设计 CSS 的时候，我们需要考虑书写的条理是否清晰，后续修改内容是否低成本，新增内容又是否会打破现有逻辑。而在组织 CSS 的时候，尽量地多考虑下抽象与解耦，可以依据现有的内容，快速创建新的模块。

而现在对于 CSS 模块化的解决方案其实也有很多，比如 `OOCSS`，`SMACSS`，`CSS Modules` 以及 `BEM` 的命名规则。在这里，我也不一一介绍这些概念具体是什么了，有兴趣的同学可以自己去研究下，相关的文章已经太多了。

在如此多的选择之中，甚至可以说是混乱，我觉得最重要的是，各个团队应该依据各自的业务情况选择合适的方案，并做好团队中的**约定**。大家遵循同一套约定与规范，在合理解决问题的同时也避免出现使用混乱的情况。

下面来谈谈我自己平时项目中的一些解决方案。这也是平时开发经验和借鉴现有方案的组合。

闲话扯在前面的是，目前方案中推荐使用预处理器等方式。虽然即使不用也能满足绝大多数场景，但是有更优雅而便捷的方式何乐而不为呢。

首先，目录划分。将 CSS 分为两类，通用模块和业务模块，可以适当地分为多个小文件。

* 通用模块中存放公共部分，同一类别独自一文件，可以是表示结构的，也可以是表示主题的，唯独表示页面具体逻辑的部分，不能放在该目录下。
* 业务模块则指具体页面对应的 CSS 文件。一个页面单独对应一个 CSS 文件。通过 `@import` 引入相关的通用模块子文件及需要更细粒度拆分的 CSS 文件。

如下图所示，`common` 中存在的为共用部分文件，而 `page` 下则具体对应到指定的页面。`Demo/style.css` 为对应 `Demo` 页面中的 CSS 文件。其中 `style.css` 可能还 `@import` 了 `common/form.css`，`common/btn.css` 以及 `Demo/css` 目录下的文件。

        ├── common
        │   ├── btn.css
        │   ├── common.css
        │   ├── form.css
        │   └── layout.css
        └── page
            └── Demo
                ├── css
                │   ├── list.css
                │   └── search.css
                └── style.css

其次，在命名方式上，主要以原子类 + BEM 的方式来命名。
这里的原子类，不是像 `mr10`， `pull-right` 这类方式，而是带有固有前缀的命名方式。好处是可以通过命名来快速识别所属位置及功能。比如以 `ly-` 前缀来表示和页面布局相关结构，如两栏三栏。再比如 `pos-mr10`，`pos-fl` ，`sty-fiz12`等等。这个可能目前需要团队内约定。

至于具体到达一个组件级别的时候，直接推荐使用 BEM 方式来维护。BEM 真心强大，虽然初识乱花渐欲迷人眼。BEM 后续再单独拆出来讲吧。

来看一些代码的片段加强理解。

```css
$orange: #fe7800;

@function Mpercentage($value){
    @return percentage($value/640);
}

// btn.css
.btn-full{
    display: inline-block;
    width: 100%; height: 48px; line-height: 48px;
    text-align: center; font-size: 1.8rem;
}
.btn-inline{
    display: inline-block;
    padding: 0 Mpercentage(60);
    height: 30px; line-height: 30px;
}
.btn-purple{
    color: #fff;
    background: #9a78c8;
}
.btn-white{
    color: #666;
    background: #fff;
}

// login.css
.login-form{
    font-size: 1.4rem;

    &__item{
        position: relative;
        margin-bottom: Mpercentage(20);
    }
    &__item_btn{
        margin-top: Mpercentage(30);
    }
    &__item_tips{
        line-height: 35px; font-size: 1.3rem;
        color: #666;
    }
    &__input{
        display: block;
        padding: 0 3%;
        width: 93%; height: 43px; line-height: 43px;
        font-size: 1.5rem;
        border: 1px solid #dadada;

        &:focus{
            border-color: #baacdf;
        }
        &.form-error{
            border-color: $orange;
        }
    }
}
```

最后，通过函数、组件模块的抽离来实现页面的快速重组。这里其实蛮多都依赖到预处理器的特性了，从而使得 CSS 可以变得更加灵活。

至此，主要概括起来就是通过文件与命名来解决冲突问题，通过预处理器来解决维护扩展问题。

那么问题来了，你发现了上述中几个不能满足的场景了么？


