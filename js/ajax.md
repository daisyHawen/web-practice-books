# 5.ajax

---

AJAX的翻译过来就是Asynchronous Javascript and XML（异步的javascript 和XML）

XMLHTTPRequest是AJAX的基础

所有现代浏览器（IE7+、Firefox、Chrome、Safari以及Opera）均内建XMLHttpRequest

1.创建一个XMLHttpRequest

```js
variable = new XMLHttpRequest();

// 老版本的XMLHttpRequest对象创建语法
variable = new ActiveObject("Microsoft.XMLHttp");
```

为了适应所有版本的浏览器，需要这样创建

```js
var xmlHttp;
if (window.XMLHttpRequest) {
//IE7以上的
    xmlHttp = new XMLHttpRequest()
} else {
//IE7之前的
    xmlHttp = new ActiveObject("Microsoft.XMLHttp")
}
```

2.XHR的open函数

open函数有三个参数：方法（”get“，”post“）、请求的URL、是否使用异步（boolean）

```js
xmlHttp.open("get", "example", false)
```

open\(\)方法不会真正的发送请求，而是启动一个请求以备发送。

要发送请求必须调用send\(\)方法。

```js
xmlHttp.send(null)
```

服务器返回数据后，会自动填充xmlHttp的属性

* responseText：作为响应主体被返回的文本
* responseXml：如果响应的类容是”text/xml“或”application/xml“，这个属性中将保存着响应数据的XML DOM文档。
* status：响应的HTTP状态码
* statusText：HTTP状态的说明





