+++
categories = ['js']
date = "2017-09-28T00:47:14+08:00"
description = ""
tags = ['es6']
title = "ES6 proxy"

+++

Proxy，顾名思义就是代理，是指在目标对象前加一层拦截（traps），可用于对访问对象的拦截和过滤。那么这个和上一节讲到的 Decorator 有什么区别呢？

Decorator 是给对象添加一些额外的职责，说白了是保持原有的逻辑不变。而 Proxy 的话更倾向于为对象提供一种代理，从而控制对这个对象的访问。

先来看看基本的实例。

```javascript
let proxy = new Proxy(target, handler)
```

`target` 表示要拦截的对象，可以是 `Array`，`Object`，`Function` 或者另一个 `Proxy`。`handler` 表示要拦截的行为。而作用的结果则体现在 Proxy 的实例 `proxy` 上。如果目标没有拦截，就等同于原对象。

对于具体的 API 这里就不做展开了，需要补充的同学可以直接[点击查看](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)。无非是监听对象的操作，然后执行相应的目标行为。比如 `get`，`set`，`deleteProperty` 等等。

从一个简单的 get 操作来看看 proxy 实际是如何操作的。在取对象 obj 值的时候，记录一条日志。

```javascript
let obj = {a: 1}
let proxy = new Proxy(obj, {
    get (target, key, proxy) {
        console.log(`get target key ${target[key]}`)
        return target[key]
    }
})
console.log(proxy.a)
```

在 Proxy 之前，我们会做类似功能来模拟。通过 globalGetInterceptor 函数来实现具体的对象操作内容。

```javascript
// babel-plugin-proxy
var defaultHandle = {
    get: function(obj, propName) {
        return obj[propName]
    }
}
var Proxy = function(target, handle) {
    this.target = target
    this.handle = handle

    this.handle.get = this.handle.get || defaultHandle.get
}
Proxy.getTrap = function(propName) {
    return this.handle.get(this.target, propName)
}
function globalGetInterceptor(obj, propName) {
    if (obj instanceof Proxy) {
        return obj.getTrap(propName)
    }
    var value = defaultHandle.get(obj, propName)
    if (typeof value === 'function') {
        return value.bind(this)
    } else {
        return value
    }
}
globalGetInterceptor(proxy, 'a')
```


实际上，Proxy 的应用场景挺广的。目前看到他们常用有：
* 对对象访问的控制
* 降低函数或类的复杂度
* 对资源的检验

通过具体的例子来演示一下。

第一个 `console` 的例子，稍微扩展一下的话，就可以当做监控来使用。比如在 get 或者 set 方法中加入对应的埋点等操作等等。

还是用上面讲到的 obj 对象。我们也可以做一些校验，访问控制等等。比如我们只允许 obj 属性赋值是数字。不然就报错。例子如下：

```javascript
let obj = {a: 1}
let proxy = new Proxy(obj, {
    set (target, key, newValue) {
        if (isNaN(newValue)) {
            throw 'Error: isNaN'
        }
        target[key] = newValue
    }
})
proxy.b = 1
```

还有更高阶的，比如双向绑定等等。不一一例举了。















