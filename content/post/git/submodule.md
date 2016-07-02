+++
date = "2016-07-02T14:26:44+08:00"
description = ""
tags = ["git"]
categories = ["git"]
title = "挖一挖 submodule"
+++

在很长的一段时间内，对`submodule`的概念含糊不清。特别是 `submodule` 和 `subtree` 这两个的区别。今天正好有时间来理一理。

### 子模块（submodule）
为了解决项目中的依赖问题。使得更加方便的维护与管理。子模块允许你将一个 Git         仓库作为另外一个 Git 仓库的子目录，从而保持相对独立的提交记录。

子模块有点类似与一个软链。你记录他们当前确切所处的提交，但是不能记录一个子模块的 `master` 或者其它符号的引用。在 `git` 的存储信息中，`submodule` 的属于 `160000` 模式，这在Git中是一个特殊模式，基本意思是你将一个提交记录为一个目录项而不是子目录或者文件。

#### 用法
`submodule` 的使用还是挺方便的。`.gitmodules` 文件保存着项目 URL 与已经拉取的本地目录之间的映射。

`git submodule add` 来增加新模块，`git submodule init` 用于初始化，`git submodule update` 用于更新，就是删除的时候不太方便。
而另外一个更新维护子模块的时候也有些繁琐，也常常会出一些坑。

### 挖坑说明
* 当你 `pull master` 之后，如果 `submodule` 有变更，都需要记得`submodule update`一下，不然又会提交旧的信息回去了。这本质上，都是因为它在主`master`上都是已 `link` 的方式存在。

* `submodule` 的时候也有点费劲的其实。当你进入 `submodule` 的目录，其实不会切到任何分支，`HEAD` 处于游离的状态，所以在改动之前需要 `checkout master`，再来改动。

### subtree
上面讲了这个多，其实官方推荐使用 `subtree` 来代替 `submodule`。两者的区分在于，`submodule`是把一个 `commit` 嵌入到当前项目的子目录中，只相当于一个 `Link`，而 `subtree` 是合并子项目到当前项目的子目录中，是一个完整项目的拷贝。

`subtree` 除了命令长一点外，还是有挺多优点的。比如

* clone 的时候不需要 `init`
* 没有类似的 `.gitmodule` 文件
* 管理上更加方便一些

#### 用法
`subtree` 的使用相对复杂一些。这个依旧省略了各种操作的说明。具体翻看 API。

* 关联：

        git remote add -f <子仓库名> <子仓库地址>
        git subtree add --prefix=<子目录名> <子仓库名> <分支> --squash

* 更新

        git fetch <远程仓库名> <分支>
        git subtree pull --prefix=<子目录名> <远程分支> <分支> --squash

* 推送

        git subtree push --prefix=ai ai master

### 参考文献
* [Git 工具 - 子模块](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)
* [Git 工具 - 子树合并](https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A0%91%E5%90%88%E5%B9%B6)
* [Difference between git submodule and subtree](http://stackoverflow.com/questions/31769820/differences-between-git-submodule-and-subtree)
* [git subtree - a better alternative to git submodule](http://alistra.ghost.io/2014/11/30/git-subtree-a-better-alternative-to-git-submodule/)
