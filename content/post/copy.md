+++
draft = true
title = "无标题文档"
date = "2015-01-14"
tags = ["js"]
+++

省去千字各浏览器对于复制相关的 API ..

### 复制

* 可使用三方插件 [zeroclipboard](https://github.com/zeroclipboard/jquery.zeroclipboard) （flash）
* 无flash 版 [clipboard.js](https://github.com/zenorocha/clipboard.js) (未尝试使用过.... 另不确定移动支持性)
* 简单实现方案。input+execCommand (兼容性有限, 更完整的可采用下面更改文本的兼容方式)

```javascript
$('#link_copy').on('click', function(){
    $input.get(0).select();
    document.execCommand("copy");
    alert('复制成功');
});
```

### 更改复制文本内容

```javascript
var ClipBoard = function() {
        this.init();
    };
    ClipBoard.prototype = {
        init: function() {
            $('.editor-style').on('copy', this.copyCallback.bind(this));
            $('.detail-title').on('copy', this.copyCallback.bind(this));

        },
        copyCallback: function(event) {
            var href = location.href,
                author = $.trim($('.detail-title .author').text()),
                // text = event.target.innerText;
                text = this.getSelectText();

            if (typeof event.originalEvent.clipboardData === 'object') {
                event.originalEvent.clipboardData.setData('text/html', this.resetTextHtml(href, author, text));
                event.originalEvent.clipboardData.setData('text/plain', this.resetText(href, author, text).join('\n'));

                if (event.originalEvent.clipboardData.getData('text/plain').length > 0) {
                    // preventDefault 不能通通阻止
                    event.preventDefault();
                    return false;
                }
            }
            // iOS 不支持 clipboardData.setData 方法
            if (window.getSelection) {
                var $clipboard = $('<div>').html(this.resetText(href, author, text).join('\n'))
                                    .css({
                                        position: 'fixed',
                                        left: '-9999px'
                                    });

                $clipboard.appendTo('body');
                window.getSelection().selectAllChildren($clipboard.get(0));
                setTimeout(function() {
                    $clipboard.select();
                    // $clipboard.remove();
                }, 200);
            }
            // IE
            if (window.clipboardData) {
                window.clipboardData.setData('text', this.resetText(href, author, text).join('\n'));
            }
        },

        /**
         * 鼠标划词取词
         * @return {String} 选中的词
         */
        getSelectText: function() {
            // IE
            if (document.selection) {
                return document.selection.createRange().text;
            } else {
                return window.getSelection().toString();
            }
        },
        /**
         * 重置剪切板文案内容 HTML 格式
         * 参数同 resetText
         */
        resetTextHtml: function(href, author, text) {
            return '\x3cdiv\x3e' + this.resetText(href, author, text).join('\x3cbr /\x3e') + '\x3c/div\x3e';

        },
        /**
         * 重置剪切板文案内容
         * @param  {String} href   本文链接
         * @param  {String} author 本文作者
         * @param  {String} text   复制的内容
         * @return {Array}        返回拼接后的内容是数组格式
         */
        resetText: function(href, author, text) {
            text = text || '';
            return ['以下内容来自「丁香医生」，原文链接：' + href,
                    '',
                    text,
                    '查看更多：' + href,
                    '',
                    '作者：' + author,
                    '本文已通过丁香医生审稿专家委员会同行评议/独家授权，未经许可不得转载。'];
        }
    };
```

#### 测试相关
* 未测 IE8
* Android 未完全测试

#### 已经问题
* 红米微信未触发 copy 事件，未深究
* iOS 不支持 clipbordData.setData 方法，改用其它

### 禁止复制

```javascript
$('body').attr('onselectstart','return false;')
                    .attr('oncopy', 'return false;')
                    .attr('oncut', 'return false;')
                    .css('-moz-user-select','none')
                    .css('-webkit-user-select','none')
                    .css('user-select','none');
```

