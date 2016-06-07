+++
categories = ["hybird"]
date = "2016-02-29T23:27:11+08:00"
description = ""
draft = true
tags = ["app", "js", "hybird"]
title = "hybird 学习总结"

+++

备注： 该篇为学习整理篇。其实个人整体上移动 App 相关开发实践不多，希望以后能有一些机会多做尝试。

### 兴起
随着近几年移动端的不断兴起，App 的开发在这几年打的热火朝天。但是对于一个普通的小团队来说，要维护 IOS, Android, Web 等各个平台，也算是个不小的开支。能够实现一套方案来跨平台的开发，这无疑是一种福音。而且也有很多人去尝试解决这个问题，也有各种五花八门的解决方案。

目前一些主流跨平台方案。
~~其实我没能特别明白下面的一些差异性，在我的角度看来，实际上都是通过将一种语言转换成另外一种的过程。~~

编程语言翻译/容器兼容/Semi-hybrid App/Hybrid App/Mobile Web

挑今天的重点说，Hybird
是混合了 Web 和 Native 的开发模式，本质上是一个 WebView，偶尔会通过 JSBridage 操作原生方法。

### 优缺点
* 便利。多平台支持
* 体验一致
* 更新方便

缺点：

* 体验和 Native 有差距
* 安全性差
* 不适合做功能复杂的应用

### Native 和 H5
* Native 的方式，不够灵活，每次发布需要发版本 (动画，多线程，IO 性能)
* Web 无法实现 Native 般流畅，无法调用系统 API

#### 职责划分
Native

* 关键性页面。交互性强页面(注册，登录，支付)
* 导航组件
* 系统级 UI (alert, loading)
* 默认界面（404.. 无网络）

### 关于 JSBridage
[WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge) 是一个不错的JavaScript与Native之间双向通信的库。

#### 原理
分为三个阶段：

* Native 向 Native Bridge 发消息
    - 把要调用的 JS 的 handler name 和 callback 传给 Native Bridge。Native Bridge 生成 callback id
* JS 执行完毕后通知 Native Bridge
    - Native Bridge 将消息发送到 JS Bridge，JS Bridge 根据 handler name 找到合适的 handler 并执行，执行完毕后将消息存到消息队列中，并通知 Native Bridge（handler 可能是异步的）。
* Native Bridge 从 JS 中取回消息，执行callback

IOS 中通过 UIWebView 的 stringByEvaluatingJavaScriptFromString

        - (NSString *)stringByEvaluatingJavaScriptFromString:(NSString *)script;


### Hybrid 更新机制
有多种方案选择。

* 开发可使用代理访问开发机
* H5 代码推送到管理平台进行构建，打包
* 管理平台可通用事先设计好的长链接将新版信息推送到客户端
* 客户端收到更新指令后开始下载新包、对包进行完整性校验、merge回本地对应的包，更新结束。

其中，管理平台推送给客户端的信息主要包括项目名（包名）、版本号、更新策略（增量or全量）、包CDN地址、MD5等。

![img](post/hybrid-common/update-workflow.png)

其它

资源文件分为经常更新的业务代码和不经常更新的框架，库，共用代码等。可以分开存储。

### 读文件


### 三方库
随便搜索了一下，网上关于 Hybrid 的框架很多很多..如 IONIC，Cordova/PhoneGap



1、背景

2、WebviewJavascriptBridge 原理

Native 向 JS 传递消息
JS 向 Native 传递消息
解决一些边缘情况（大数据量，并发等等）
2.1、 IOS 与 JS 通信

OC 有现成接口向 JS 传递消息
webview 提供了 stringByEvaluatingJavaScriptFormString 方法执行 JS
2.2、 Android 与 JS 通信

Android 通过类似加载 view 的方式去执行 JS （见 PDF）
暂时没有回传 Android 的方法
2.3、 JS 向 Native 传递消息的方法

webview 监控页面 url 变化来获取（IOS/Android）
JS prompt() 的方式接收（Android）
服务器端。采用 get/post 接口方式（IOS/Android）
其它..
3、边缘情况

稳定的信息通道
大数据量的信息传递
Bridge 可用性监测
频繁的信息传递
并发
... （解决方案具体见 PDF）

4、 DXY 内部 JS-Native Bridge

目前已实现一些基础信息功能。等待后续扩充。具体使用方式可见文档。

5、面临的问题

多数是在过渡期如何处理



微信JS-SDK

APP 可以在跳转的时候拦截 URL

一般来讲，也是我目前知道的两种主流的方式就是
js调用Native中的代码
Schema：WebView拦截页面跳转

### 扩展阅读
* [跨平台移动开发与Hybrid学习笔记](http://johnwong.github.io/mobile/2015/04/20/cross-platform-and-hybrid.html)
* [Hybrid APP架构设计思路](https://github.com/chemdemo/chemdemo.github.io/issues/12)
* [使用ionic框架开发移动hybrid应用](https://segmentfault.com/a/1190000002688632)
* [谈谈App混合开发](http://bxbxbai.gitcafe.io/2015/08/16/talk-about-bybird-app/)
* [HYBRID MOBILE APPS SUCK A LOT LESS](https://wiredcraft.com/blog/hybrid-mobile-apps-suck-a-lot-less/)
* [【Web缓存机制系列】6 – 进击的Hybrid App，量身定做缓存机制](http://www.alloyteam.com/2013/12/web-cache-6-hybrid-app-tailored-cache/)
* [Hybrid 架构下的 H5 应用加速方案](http://www.aliued.cn/2014/03/02/hybrid-%E6%9E%B6%E6%9E%84%E4%B8%8B%E7%9A%84-h5-%E5%BA%94%E7%94%A8%E5%8A%A0%E9%80%9F%E6%96%B9%E6%A1%88.html)
* [Hybrid选型及设计原理](https://segmentfault.com/a/1190000002587623)

