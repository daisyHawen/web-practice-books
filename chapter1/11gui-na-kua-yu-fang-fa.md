## 10.跨域方法总结

---

同源策略是XHR的一个主要约束，它为通信设置了“相同的域，相同的端口，相同的协议”这一限制

## 1.跨域资源共享CORS

CORS是W3C的一个工作草案，定义了在必须访问跨域资源时，浏览器于服务器应该如何沟通。CORS背后的思想是，使用自定义的HTTP头部让浏览器和服务器进行沟通，从而决定请求或响应是否应该成功或者失败。

比如一个简单的请求使用GET或者POST，他没有自定义头部，而主体内容时text/plain，在发送时就要额外给他添加一个Origin头部，其中包含请求页面的源信息（协议、域名/端口），以便服务器根据头信息决定是否给予响应。如下：

```
Origin:http://www.nzonline.net
```

如果服务器认为这个请求是合理的就在Access-Control-Allow-Origin头部发相同的源信息（如果是公共资源，可以回发“\*”）

```
Access-Control-Allow-Origin:http://www.nzonline.net
```

## 2.图像Ping

一个网页可以从任何网页中加载图像，不用担心跨不跨域。使用它们的onload和onerror事件处理程序可以确定是否接收到了正确的响应。

## 3.JSONP

JSONP实质上是一种Hack技术，它的原理就是利用图像ping，而且只有Get才能够使用。

## 4.Comet

Coment是对Ajax的进一步扩展，让服务器几乎能够实时地向客户端推送数据。实现Comet地技术主要有：长轮询和HTTP流。所有浏览器都支持长轮询，而只有部分浏览器原生支持HTTP流。SSE（Server-sent Events，服务器发送事件）是一种实现Comet交互地浏览器API，既支持长轮询，也支持HTTP流

