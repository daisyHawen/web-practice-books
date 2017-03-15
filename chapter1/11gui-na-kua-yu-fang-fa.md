## 10.跨域方法总结

---

同源策略是XHR的一个主要约束，它为通信设置了“相同的域，相同的端口，相同的协议”这一限制

1.jsonp 需要目标服务器配合一个callback函数。

2.window.name+iframe 需要目标服务器响应window.name。

3.window.location.hash+iframe 同样需要目标服务器作处理。

4.html5的 postMessage+ifrme 这个也是需要目标服务器或者说是目标页面写一个postMessage，主要侧重于前端通讯。

5.[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS) 需要服务器设置header ：`Access-Control-Allow-Origin。`

6.**nginx反向代理**这个方法一般很少有人提及，但是他可以不用目标服务器配合，不过需要你搭建一个中转nginx服务器，用于转发请求。

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

---

子页面间地跨域访问

### 5.通过修改document.domain来跨子域

我们只需要在跨域的两个页面中设置document.domain就可以了。修改document.domain的方法只适用于不同子域的框架间的交互。

例如：1.在页面 http:// www.haorooms.com/a.html 中设置document.domain

```
<iframe id = "iframe" src="http://haorooms.com/b.html" onload = "test()"></iframe>
<script type="text/javascript">
    document.domain = 'haorooms.com';//设置成主域
    function test(){
        alert(document.getElementById('￼iframe').contentWindow);//contentWindow 可取得子窗口的 window 对象
    }
</script>
```

2、在页面http:// haorooms.com/b.html 中设置document.domain

```
<script type="text/javascript">
    document.domain = 'haorooms.com';//在iframe载入这个页面也设置document.domain，使之与主页面的document.domain相同
</script>
```

### 6.使用window.name来进行跨域

window对象有个name属性，该属性有个特征：即在一个窗口\(window\)的生命周期内,窗口载入的所有的页面都是共享一个window.name的，每个页面对window.name都有读写的权限，window.name是持久存在一个窗口载入过的所有页面中的。

方法：

假如有三个页面。

```
a.com/app.html：应用页面。
a.com/proxy.html：代理文件，一般是一个没有任何内容的html文件，需要和应用页面在同一域下。
b.com/data.html：应用页面需要获取数据的页面，可称为数据页面。
```

**1、在应用页面（a.com/app.html）中创建一个iframe，把其src指向数据页面（b.com/data.html）。**

数据页面会把数据附加到这个iframe的window.name上，data.html代码如下：

```
<script type="text/javascript">
    window.name = 'I was there!';    // 这里是要传输的数据，大小一般为2M，IE和firefox下可以大至32M左右
                                     // 数据格式可以自定义，如json、字符串
</script>
```

2.**在应用页面（a.com/app.html）中监听iframe的onload事件，在此事件中设置这个iframe的src指向本地域的代理文件（代理文件和应用页面在同一域下，所以可以相互通信）。**

app.html

```
<script type="text/javascript">
    var state = 0, 
    iframe = document.createElement('iframe'),
    loadfn = function() {
        if (state === 1) {
            var data = iframe.contentWindow.name;    // 读取数据
            alert(data);    //弹出'I was there!'
        } else if (state === 0) {
            state = 1;
            iframe.contentWindow.location = "http://a.com/proxy.html";    // 设置的代理文件
        }  
    };
    iframe.src = 'http://b.com/data.html';
    if (iframe.attachEvent) {
        iframe.attachEvent('onload', loadfn);
    } else {
        iframe.onload  = loadfn;
    }
    document.body.appendChild(iframe);
</script>
```

3.**获取数据以后销毁这个iframe，释放内存；这也保证了安全（不被其他域frame js访问）。**

```
<script type="text/javascript">
    iframe.contentWindow.document.write('');
    iframe.contentWindow.close();
    document.body.removeChild(iframe);
</script>
```

## 7.HTML5的  Window.postMessage方法跨域

### 8.运用反向代理

---

运用ngix实现反向代理：http://www.cnblogs.com/gabrielchen/p/5066120.html

