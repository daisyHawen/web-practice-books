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

**状态码304表示请求的资源没有被修改，可以直接使用浏览器中缓存的版本**

3.XHR的readyState

敲重点！！！真正的异步来了。

发送异步请求，可以检测XHR的readyState属性，该属性表示请求/响应过程中当前活动阶段。取值有：

* 0：未初始化。尚未调用open\(\)方法
* 1：启动。已经调用了open\(\)方法，但是没有调用send\(\)方法
* 2：发送。已经调用了send\(\)方法，但是尚未接收到响应
* 3：接收。已经收到部分响应数据
* 4：完成。已经接收全部响应数据，而且已经可以在客户端使用了。

同时，每当readyState由一个状态变为另一个状态，都会触发readystatechange事件。

```js
xmlHttp.onreadystatechange = function() {
	if (xmlHttp.readyState == 4) {
		if (xmlHttp.status >= 200 && xmlHttp.status < 300 || xmlHttp == 304) {
			alert(xmlHttp.responseText);
		} else {
			alert("Request was unsuccessful:" + xhr.status);
		}
	}
}
```



