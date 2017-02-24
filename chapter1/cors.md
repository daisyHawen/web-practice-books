## 一、有src的标签

说明：所有具有`src`属性的HTML标签都是可以跨域的，包括`<img>`,`<script>。`

限制：需要创建一个DOM对象，只能用于GET方法。

在`document.body`中`append`一个具有`src`属性的HTML标签，`src`属性值指向的URL会以GET方法被访问，该访问是可以跨域的。

其实样式表的&lt;link&gt;标签也是可以跨域的，只要是有 **src **或者 **href** 的HTML标签都有跨域的能力。

### 不同的HTML标签发送HTTP请求的时机不同

例如`<img>`在更改`src`属性时就会发送请求，而`script`,`iframe`,`link[rel=stylesheet]`只有在添加到DOM树之后才会发送HTTP请求：

```js
var img = new Image();
img.src = 'http://some/picture';        // 发送HTTP请求

var ifr = $('<iframe>', {src: 'http://b.a.com/bar'});
$('body').append(ifr);                  // 发送HTTP请求
```

## 二、JSONP

jsonp是JSON with padding的简写，看起来与json差不多，但是包含在函数调用中的json，利用动态script元素来使用（具有src属性的如img,iframe,srcipt都不受同源策略的影响）。该协议的一个要点就是允许用户传递一个callback参数给服务端，然后服务端返回数据时会将这个callback参数作为函数名来包裹住JSON数据，这样客户端就可以随意定制自己的函数来自动处理返回数据了。

如果使用jquery，可以在type为get的时候dataType设为jsonp,就可以了。例如：

```js
$.ajax({
        url: 'http://www.qdaily.com/get_user_and_radar.json?winWidth=1280&winHeight=800',
        type: 'get',
        dataType: 'jsonp',
        data: {},
    })
    .done(function(data) {
        console.log(data.status);
    })
```

`$.getJSON`与`$.get`的区别是前者会把`responseText`转换为JSON，而且当URL具有`callback`参数时， jQuery将会把它解释为一个JSONP请求，创建一个`<script>`标签来完成该请求。

【总结下jsonp】:

* 优点：
  简单，函数回调在本地处理；
* 缺点：
  1、安全性（存在注入漏洞，如CSRF,XSS）；
  2、如果出现错误，不会像http请求那样有状态码；
  3、只能使用get请求；

# 



