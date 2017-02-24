# 什么是跨域

当一个资源请求一个其它域名的资源时会发起一个跨域HTTP请求\(cross-origin HTTP request\)。

比如说，域名A\([http://domaina.example\\)的某](http://domaina.example\)的某\) Web 应用通过&lt;img&gt; 标签引入了域名B\([http://domainb.foo\\)的某图片资源\\(http://domainb.foo/image.jpg\\)，域名A的](http://domainb.foo\)的某图片资源\([http://domainb.foo/image.jpg\\)，域名A的](http://domainb.foo/image.jpg\)，域名A的\)\) Web 应用就会导致浏览器发起一个跨域 HTTP 请求。

在当今的 Web 开发中，使用跨域 HTTP 请求加载各类资源（包括CSS、图片、JavaScript 脚本以及其它类资源），已经成为了一种普遍且流行的方式。

如果没有做任何限制，在本地通过ajax访问一个跨域源，查看某些浏览器（chrome、firefox）的控制台，就会发现报错。

就会报这种错误

> XMLHttpRequest cannot load [http://192.168.0.25:8080/JsonAccept/accept](http://192.168.0.25:8080/JsonAccept/accept). Response to preflight request doesn’t pass access control check: No ‘Access-Control-Allow-Origin’ header is present on the requested resource. Origin ‘null’ is therefore not allowed access

## 什么时候会出现跨域 {#什么时候会出现跨域}

| URL（A） | URL（B） | 说明 | 是否允许跨域 |
| :--- | :--- | :--- | :--- |
| [http://www.a.com/a.js](http://www.a.com/a.js) | [http://www.a.com/b.js](http://www.a.com/b.js) | 同一域名下 | 是 |
| [http://www.a.com/lab/a.js](http://www.a.com/lab/a.js) | htttp://www.a.com/scripyt/b.js | 同一域名下，不同文件夹 | 是 |
| [http://www.a.com:8000/a.js](http://www.a.com:8000/a.js) | [http://www.a.com/b.js](http://www.a.com/b.js) | 同一域名下，不同端口 | 否 |
| [http://www.a.com/a.js](http://www.a.com/a.js) | https//www.b.com/b.js | 同一域名下，不同协议 | 否 |
| [http://www.a.com/a.js](http://www.a.com/a.js) | [http://192.89.90.112/b.js](http://192.89.90.112/b.js) | 域名和端口IP对应 | 否 |
| [http://www.a.com/a.js](http://www.a.com/a.js) | [http://script.a.com/b.js](http://script.a.com/b.js) | 主域相同，子域不同 | 否 |
| [http://www.a.com/a.js](http://www.a.com/a.js) | [http://a.com/b.js](http://a.com/b.js) | 同一域名，不同二级域名 | 否（cookie也不允许访问） |
| [http://www.a.com/a.js](http://www.a.com/a.js) | [http://www.b.com/b.js](http://www.b.com/b.js) | 不同域名 | 否 |

# 跨域解决方案

## 一、JSONP

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

【总结下jsonp】:

* 优点：
  简单，函数回调在本地处理；
* 缺点：
  1、安全性（存在注入漏洞，如CSRF,XSS）；
  2、如果出现错误，不会像http请求那样有状态码；
  3、只能使用get请求；

## 二、有src的标签



