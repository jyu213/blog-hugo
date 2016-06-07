+++
draft = true
layout = "post"
title = "HTTP 缓存"
date = "2015-12-27"
tags = ["http", "浏览器"]
+++

关于缓存的概念，早已经有很多的文章来细致的讲解过了，这里，只做知识整理。

注： 此处的 HTTP 协议为 1.1 版本。2.0 的暂时没有做过研究。

### 概念
跳过概念性内容。简明描述就是资源的一个副本。缓存的好处显而易见，减少页面加载时间,减少服务器负载。

### 类别
缓存是无处不在的，有浏览器端，服务器端，代理服务器端（如 cdn），页面，对象，甚至数据库。基本上每个 Web 的流程上都会设置一道关卡。不过随之而来的，就是在发布的时候就有可能需要多一套甚至几套手续去清除他的缓存。

* 数据库缓存
	- 假使频繁操作数据库查询，容易 carsh，可将查询后的数据放到内存中缓存，提高响应。如 memcached
* CDN 缓存
	- 一般指多台的负载均衡的源服务器。浏览器首先向 CDN 发起请求，CDN 会根据负载情况，动态将请求转发到合适的源服务器上。一般上 CDN 供应商也是遵循 HTTP 标准协议。
* 代理服务器
	- 浏览器和服务器间的中间服务器。浏览器先向这个中间服务器发起请求，再转发到对应的源服务器上。类似浏览器的缓存。只是规模更大。可以理解为局域网。
* 浏览器缓存
	- 浏览器在本地的一个副本，不同的浏览器可能有差别，但基本上是遵循 HTTP 协议缓存机制。
* 应用层缓存
	- 代码层面缓存。如把请求过的数据存起来，再次需要的时候选择缓存数据。如 JS 变量来缓存 ajax 请求数据。

### 浏览器端缓存规则
浏览器端依据`HTTP协议头`和`HTML页面的Meta标签`来判断。分别从`新鲜度`和`校验值`来判断是否使用副本。
新鲜度指的是：
* 含有完整的过期时间控制头信息，且仍在有效期内
* 浏览器已使用过这个缓存副本，且在一个会话中已经检查过新鲜度

校验值指的是服务器返回信息中带的`ETag`标签。用于匹配文件是否已修改。

### HTTP 缓存机制
浏览器请求一个静态资源文件的时候，
1. 首先会检测本地缓存，如果本地资源没过期，直接使用本地文件，而不会发送请求给服务器
2. 如果找到资源文件，但不知道是否过期，则发送请求到服务器，如返回 304 则使用本地资源
3. 如果服务器发现资源已修改或者是新的，则返回 200，更新替换本地文件，如服务器上没有，返回 404

Web 服务器通过两种方式来判断浏览器缓存是否最新。

* 浏览器把缓存文件的最后修改时间通过 `header If-Modified-Since` 来告诉服务器
* 浏览器把缓存文件的 `ETag` 通过 `header If-None-Match` 来告诉服务器

此处应该有图片

### 缓存相关的 header
Request 阶段

|Cache-Control|max-age=0|以秒为单位|
|If-Modified-Since|Mon, 19 Nov 2012 08:38:01 GMT|缓存文件的最后修改时间|
|If-None-Match|"0693f67a67cc1:0"|缓存文件的Etag值|
|Cache-Control|no-cache|不使用缓存|
|Pragma|no-cache|不使用缓存|

Response 阶段

|Cache-Control|public|响应被缓存，并且在多用户间共享|
|Cache-Control|private|响应只能作为私有缓存，不能在用户之间共享|
|Cache-Control|no-cache|提醒浏览器要从服务器提取文档进行验证|
|Cache-Control|no-store|绝对禁止缓存（用于机密，敏感文件）|
|Cache-Control|max-age=60|60秒之后缓存过期（相对时间）|
|Date|Mon, 19 Nov 2012 08:39:00 GMT|当前response发送的时间|
|Expires|Mon, 19 Nov 2012 08:40:01 GMT|缓存过期的时间（绝对时间）|
|Last-Modified|Mon, 19 Nov 2012 08:38:01 GMT|服务器端文件的最后修改时间|
|ETag|"20b1add7ec1cd1:0"|服务器端文件的Etag值|

同时存在cache-control和Expires 优先考虑 cache-control

### ETag
ETag是实体标签（Entity Tag）的缩写， 根据实体内容生成的一段hash字符串，可以标识资源的状态。
用于解决：
* 某些服务器不能精确到文件的最后修改时间
* 文件修改频繁(1秒以内)
* 文件最后时间变了，内容未变

### 用户行为
* ctrl + F5 强制刷新
* F5 刷新，浏览器会去 Web 服务器验证缓存
* 地址栏回车，浏览器会直接使用有效的缓存(叫做缓存命中)
* 浏览器的刷新和。
* 页面中的链接跳转
* 前进，后退

### 如何清除缓存
* 更改链接地址（加时间戳）

### 如何利用缓存机制
* 在不同的网址上使用一致的网址。如 jquery cdn
* ETag 验证
* 确定哪些资源可以使用代理缓存。对所有用户完全相同的资源，如图片，css..
* 确定最优资源缓存周期
* 确定网站最佳缓存层级
* 变动最小化， 有些特定资源经常变（业务类 js）,有些不变（组件，库）

无法被浏览器缓存的请求：
* HTTP信息头中包含Cache-Control:no-cache，pragma:no-cache，或Cache-Control:max-age=0等告诉浏览器不用缓存的请求
* 需要根据Cookie，认证信息等决定输入内容的动态请求是不能被缓存的
* 经过HTTPS安全加密的请求（有人也经过测试发现，ie其实在头部加入Cache-Control：max-age信息，firefox在头部加入Cache-Control:Public之后，能够对HTTPS的资源进行缓存，参考《HTTPS的七个误解》）
* POST请求无法被缓存
* HTTP响应头中不包含Last-Modified/Etag，也不包含Cache-Control/Expires的请求无法被缓存

### 其它方式
* Expires, Web服务器响应消息头字段，在响应http请求时告诉浏览器在过期时间前浏览器可以直接从浏览器缓存取数据，而无需再次请求。HTTP 1.0 的内容，可以忽略。
* HTML Meta 标签。设置 content 为 no-cache。不过该方法不被所有浏览器支持，所以不推荐。

### HTML5
还可以使用离线应用Manifest，localstorage等等。

// TODO: https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn

### 参考文献
* [Leverage Browser Caching - Google][1]
* [Caching Tutorial][2]
* [【Web缓存机制系列】2 – Web浏览器的缓存机制][3]
* [翻译：web制作、开发人员需知的Web缓存知识][4]
* [H5 缓存机制浅析 - 移动端 Web 加载性能优化][5]

[1]:	https://developers.google.com/speed/docs/insights/LeverageBrowserCaching
[2]:	https://www.mnot.net/cache_docs/
[3]:	http://www.alloyteam.com/2012/03/web-cache-2-browser-cache/
[4]:	http://www.zhangxinxu.com/wordpress/2013/05/caching-tutorial-for-web-authors-and-webmasters/
[5]:	http://segmentfault.com/a/1190000004132566