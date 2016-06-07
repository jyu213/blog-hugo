+++
layout = "post"
title = "html5 Notifications API"
tags = ["html5"]
+++

html5 Notifications API 出来也有一段时间了，中间规范要点也变动过好几次，也有几个不同版本。目前仍然是 W3C 的工作草案。刚好最近有项目上应用到，故简单整理一下相关内容作为备忘。注： 本文下面讲解的方法为现阶段草案内容，并不排除后期会做变更。

### 定义
`Web notifications defines an API for end-user notifications. A notification allows alerting the user outside the context of a web page of an occurrence, such as the delivery of email.` 引自[W3C notifications](http://www.w3.org/TR/notifications/)。

简单来说，就是web 端的消息提醒功能。最有印象的应该的 web版 gamil 的邮件提醒了吧。当然这个在桌面软件下早已经不陌生了的吧。不过目前好像还是不支持自定义样子之类的，所以在 UI 上各浏览器呈现具有一定的差异。

### 方法
`window`对象提供了一个`Notification` 属性。允许我们提供创建一个通知的实例。并且接收两个参数： 包含通知标题（字符串,**必需**）， 配置对象的参数。

配置对象参数包含如下：
* `body`: 字符串。进一步定义通知的目的
* `lang`: 指定通知的语言类型。必须遵守[ BCP 47 规范](http://tools.ietf.org/html/bcp47)
* `dir`: 定义消息文字显示方向。`ltr` 或 `rtl`
* `tag`: 字符串。作为可使用的检索，替换，删除的通知的 ID
* `icon`: 图片链接，用于通知的图标

例如，实例化如下：

        var notify = new Notification('hello message', {
                body: 'welcome to the notifications API'
            });

也可作为一个通知实例的只读属性。//
`Notifications` 对象提供一个 `permission` 属性，用于包含表示当前属性对象的字符串。通知只在用户同意该权限的情况下显示。
* `denied`: 表示用户拒绝通知
* `granted`: 表示用户赋予通知的权限
* `default`: 表示用户选择未知

该 API 还提供两个方法：
* `requestPermission()`: 用来请求询问显示通知的权限。
    - 通知对象的方法，接受用户接受或者拒绝权限执行时执行执行的一个回调函数。可选。参数值可为 `defined`, `granted`, `default`。
* `close()`: 关闭通知。实例对象，不接收任何参数。

附加的事件处理函数
* `onclick`: 当用户点击通知的调用
* `onclose`: 一旦用户或者浏览器关闭通知时调用
* `onerror`: 当通知出现错误时调用
* `onshow`: 当通知出现时调用

### 兼容性
目前只有少数浏览器支持该 API。且同一浏览器不同版本下支持的事件方法可能会有差异（规范改动引起）
[点击查看](http://caniuse.com/#feat=notifications)浏览器支持情况

桌面浏览器: Chrome 22 and Firefox 22 and Safari 6+
移动浏览器: Firefox and Blackberry

### 实践
前面啰啰嗦嗦的讲完了 API 中的大概内容后，来看看实际操作中我们该如何。
注：示例代码中并未针对浏览器做各方面兼容。可参看[风车](http://yangzhuoyu.com/an-introduce-to-html5-notification/)中做了更多向下兼容。

1. 由于浏览器的支持情况有差异，所以我们先得判断用户浏览器是否支持该属性

        var NotifySupport = !!(Notification in window);

2. 询问获取浏览器通知权限

        var NotifyPermission = !!(NotifySupport && Notification.permission === 'granted');
        if(NotifySupport){
            if(NotifyPermission){
                window.Notification.requestPermission();
            }
        }else{
            console.log('your Browser is not support the notification!')
        }

3. 创建 Notification 实例

        new Notification('title', { 'body': 'content'});

### 其它
[view on github](https://github.com/jyu213/Notify)

### 参考文章
* [W3C Notifications](http://www.w3.org/TR/notifications/)
* [Chorme Notifications API](http://dev.chromium.org/developers/design-documents/desktop-notifications/api-specification)
* [MDN Notification](https://developer.mozilla.org/en-US/docs/Web/API/notification)
* [HTML5 Notification 介绍](http://yangzhuoyu.com/an-introduce-to-html5-notification/)