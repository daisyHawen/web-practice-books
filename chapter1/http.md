首先，什么叫做跨域：  
MDN上的解释：

> 当一个资源请求一个其它域名的资源时会发起一个跨域HTTP请求\(cross-origin HTTP  
> request\)。比如说，域名A\([http://domaina.example](http://domaina.example/)\)的某 Web  
> 应用通过![]()标签引入了域名B\([http://domainb.foo](http://domainb.foo/)\)的某图片资源\([http://domainb.foo/image.jpg](http://domainb.foo/image.jpg)\)，域名A的 Web 应用就会导致浏览器发起一个跨域 HTTP 请求。在当今的 Web 开发中，使用跨域 HTTP  
> 请求加载各类资源（包括CSS、图片、[JavaScript](http://lib.csdn.net/base/javascript)脚本以及其它类资源），已经成为了一种普遍且流行的方式。

我的理解是，其实跨域问题是浏览器的一个安全策略问题，**因为浏览器认为跨域的访问是不安全的，因此浏览器会限制脚本中发起的跨域请求。**  
如果没有做任何限制，在本地通过ajax访问一个跨域源，查看某些浏览器（chrome、firefox）的控制台，就会发现报错。  
就会报这种错误  
XMLHttpRequest cannot load[http://192.168.0.25:8080/JsonAccept/accept](http://192.168.0.25:8080/JsonAccept/accept). Response to preflight request doesn’t pass access control check: No ‘Access-Control-Allow-Origin’ header is present on the requested resource. Origin ‘null’ is therefore not allowed access

![](http://img.blog.csdn.net/20161212104759999?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2luYXRfMjUxMjcwNDc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "这里写图片描述")

## 什么时候会出现跨域 {#什么时候会出现跨域}

那么什么时候会出现跨域问题呢？

| URL\(A\) | URL\(B\) | 说明 | 是否允许跨域 |
| :--- | :--- | :--- | :--- |
| http://www.a.com/a.js | http://www.a.com/b.js | 同一域名下 | 允许 |
| http://www.a.com/lab/a.js | http://www.a.com/script/b.js | 同一域名下不同文件夹 | 允许 |
| http://www.a.com:8000/a.js | http://www.a.com/b.js | 同一域名，不同端口 | 不允许 |
| http://www.a.com/a.js | https://www.a.com/b.js | 同一域名，不同协议 | 不允许 |
| http://www.a.com/a.js | http://70.32.92.74/b.js | 域名和域名对应ip | 不允许 |
| http://www.a.com/a.js | http://script.a.com/b.js | 主域相同，子域不同 | 不允许 |
| http://www.a.com/a.js | http://a.com/b.js | 同一域名，不同二级域名（同上） | 不允许（cookie这种情况下也不允许访问） |
| http://www.cnblogs.com/a.js | http://www.a.com/b.js | 不同域名 | 不允许 |



# 一、CORS解决方案 {#一cors解决方案}

CORS是一个W3C标准，全称是”跨域资源共享”（Cross-origin resource sharing）。  
它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。

_前提条件：CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。_

整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。

**浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。**

**因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。**

服务器设置Access-Control-Allow-OriginHTTP响应头之后，浏览器将会允许跨域请求

## 请求方式 {#请求方式}

* 简单请求
* 复杂请求

### 简单请求 {#简单请求}

所谓的简单，是指：

只使用 GET, HEAD 或者 POST 请求方法。  
**如果使用 POST 向服务器端传送数据，则数据类型\(Content-Type\)只能是 application/x-www-form-urlencoded, multipart/form-data 或 text/plain中的一种。**  
不会使用自定义请求头（类似于 X-Modified 这种）。  
类似于下面这种情况  
![](http://img.blog.csdn.net/20161209104648199?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2luYXRfMjUxMjcwNDc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "这里写图片描述")

网络请求数据

```
请求头
GET /resources/public-data/ HTTP/
1.1
Host:
 bar
.other

User-Agent: Mozilla/
5.0
 (Macintosh
; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept:
 text/html,application/xhtml+xml,application/xml
;q=0.9,*/*;q=0.8

Accept-Language: en-us,en
;q=0.5

Accept-Encoding: gzip,deflate
Accept-Charset: ISO-
8859
-
1
,utf-
8
;q=0.7,*;q=0.7
Connection:
 keep-alive

Referer:
 http://foo
.example
/examples/access-control/simpleXSInvocation
.html
Origin:
 http://foo
.example


响应头
HTTP/
1.1
200
 OK

Date:
 Mon, 
01
Dec
2008
00
:
23
:
53
 GMT

Server:
 Apache/
2.0
.61
 
Access-Control-Allow-Origin: *
Keep-Alive: timeout=
2
, max=
100
Connection:
 Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml


```

可以看到响应头，服务器设置Access-Control-Allow-Origin: \*，这表明服务器接受来自任何站点的跨站请求。

如果服务器端仅允许来自[http://foo.example](http://foo.example/)的跨站请求，它可以返回：Access-Control-Allow-Origin:[http://foo.example](http://foo.example/)

### 预请求 {#预请求}

不同于上面讨论的简单请求，“预请求”要求必须先发送一个 OPTIONS 请求给目的站点，来查明这个跨站请求对于目的站点是不是安全可接受的。  
**这样做，是因为跨站请求可能会对目的站点的数据造成破坏。**

当请求具备以下条件，就会被当成预请求处理：

> 1、请求以 GET, HEAD 或者 POST 以外的方法发起请求。或者，使用 POST，但请求数据为  
> application/x-www-form-urlencoded, multipart/form-data 或者 text/plain  
> 以外的数据类型。比如说，用 POST 发送数据类型为 application/xml 或者 text/xml 的 XML 数据的请求。  
> 2、使用自定义请求头（比如添加诸如 X-PINGOTHER）

```
*
第一次请求
*

OPTIONS /resources/post
-here
/
 HTTP/
1.1

Host: bar
.
other
User
-Agent
: Mozilla/
5.0
 (Macintosh; U; Intel Mac OS X 
10.5
; en
-US
; rv:
1.9
.1
b3pre) Gecko/
20081130
 Minefield/
3.1
b3pre
Accept: text/html,application/xhtml
+
xml
,application/
xml
;q
=
0.9
,
*
/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Origin: http://foo.example
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-PINGOTHER

*第一次响应返回*
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER
Access-Control-Max-Age: 1728000
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain

*第二次请求*
POST /resources/post-here/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/
*
;q
=
0.8

Accept
-Language
: en
-us
,en;q
=
0.5

Accept
-Encoding
: gzip,deflate
Accept
-Charset
: ISO
-
8859
-
1
,utf
-
8
;q
=
0.7
,
*
;q
=
0.7

Connection: keep
-alive

X
-PINGOTHER
: pingpong
Content
-Type
: text/
xml
; charset
=
UTF
-
8
Referer
: http:
//foo.example/examples/preflightInvocation.html

Content
-Length
: 
55

Origin: http:
//foo.example

Pragma: no
-cache
Cache
-Control
: no
-cache
*
第二次返回
*

HTTP/
1.1
200
 OK

Date
: Mon, 
01
 Dec 
2008
01
:
15
:
40
 GMT
Server: Apache/
2.0
.61
 (Unix)
Access
-Control
-Allow
-Origin
: http:
//foo.example

Vary: Accept
-Encoding
, Origin
Content
-Encoding
: gzip
Content
-Length
: 
235

Keep
-Alive
: timeout
=
2
, 
max
=
99

Connection: Keep
-Alive

Content
-Type
: text/plain


[
Some GZIP
'd payload]


```

这种情况使用一个 OPTIONS 发送了一个“预请求”。  
Firefox 3.1 根据请求参数，决定需要发送一个“预请求”，来探明服务器端是否接受后续真正的请求。

OPTIONS 是 HTTP/1.1 里的方法，用来获取更多服务器端的信息，**是一个不应该对服务器数据造成影响的方法。**  
随同 OPTIONS 请求，以下两个请求头一起被发送：

```
Access-Control-Request-
Method
:
 POST
Access-Control-Request-Headers: X-PINGOTHER


```

请求头Access-Control-Request-Method可以提醒服务器跨站请求将使用POST方法，而请求头Access-Control-Request-Headers则告知服务器该跨站请求将携带一个自定义请求头X-PINGOTHER。这样，服务器就可以决定，在当前情况下，是否接受该跨站请求访问。

该响应表明，服务器接受了客服端的跨站请求。返回的代码：

```
Access
-Control
-Allow
-Origin
: http:
//foo.example

Access
-Control
-Allow
-Methods
: POST, GET, OPTIONS
Access
-Control
-Allow
-Headers
: X
-PINGOTHER

Access
-Control
-Max
-Age
: 
1728000


```

响应头Access-Control-Allow-Methods表明服务器可以接受POST, GET和 OPTIONS的请求方法。请注意，这个响应头类似于HTTP/1.1 Allow: response header，但仅限于访问控制的场景下。  
而响应头Access-Control-Allow-Headers则表示服务器接受自定义请求头X-PINGOTHER。  
就像Access-Control-Allow-Methods一样，Access-Control-Allow-Headers允许以逗号分隔，传递一个可接受的自定义请求头列表。

最后，响应头Access-Control-Max-Age告诉浏览器，本次“预请求”的响应结果有效时间是多久。在上面的例子里，1728000秒代表着20天内，**浏览器在处理针对该服务器的跨站请求，都可以无需再发送“预请求”**，只需根据本次结果进行判断处理。

### 附带凭证信息的请求 {#附带凭证信息的请求}

前面的跨域问题得到解决了，但是又发现一个问题，就是cookie无法携带问题。  
原因在此：  
XMLHttpRequest和访问控制功能，最有趣的特性就是，发送凭证请求（HTTP Cookies和验证信息）的功能。一般而言，对于跨站请求，**浏览器是不会发送凭证信息的。但如果将XMLHttpRequest的一个特殊标志位设置为true，浏览器就将允许该请求的发送。**  
方法：  
服务器设置

```
Access
-Control
-Allow
-Credentials
: 
true


```

另一方面，开发者必须在AJAX请求中打开withCredentials属性。

如果是用的jq的ajax请求，就在ajax请求中加上这样的语句：

```
xhrFields
: 
{
                
withCredentials
:
 true
            
},
  
crossDomain
: 
true
,


```

```
 $.ajax({
            type: 
"POST"
,
            url: url,
            contentType: 
"application/json; charset=utf-8"
,
            dataType: 
'json'
,
            xhrFields: {
                withCredentials: 
true

            },
            crossDomain: 
true
,
            success: 
function
(result)
 {

            }
        });


```

如果是XMLHTTP请求方式

```
var
 xhr = 
new
 XMLHttpRequest();
xhr.withCredentials = 
true
;


```

值得注意的是：  
如果要发送Cookie，Access-Control-Allow-Origin就不能设为星号，必须指定明确的、与请求网页一致的域名。同时，Cookie依然遵循同源政策，只有用服务器域名设置的Cookie才会上传，其他域名的Cookie并不会上传，且（跨源）原网页代码中的document.cookie也无法读取服务器域名下的Cookie。

看一下脚本交互

```
请求头
GET /resources/access-control-with-credentials/ HTTP/
1.1
Host:
 bar
.other

User-Agent: Mozilla/
5.0
 (Macintosh
; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept:
 text/html,application/xhtml+xml,application/xml
;q=0.9,*/*;q=0.8

Accept-Language: en-us,en
;q=0.5

Accept-Encoding: gzip,deflate
Accept-Charset: ISO-
8859
-
1
,utf-
8
;q=0.7,*;q=0.7
Connection:
 keep-alive

Referer:
 http://foo
.example
/examples/credential
.html
Origin:
 http://foo
.example
Cookie:
 pageAccess=
2


响应头
HTTP/
1.1
200
 OK

Date:
 Mon, 
01
Dec
2008
01
:
34
:
52
 GMT

Server:
 Apache/
2.0
.61
 (Unix) PHP/
4.4
.7
 mod_ssl/
2.0
.61
 OpenSSL/
0.9
.7
e mod_fastcgi/
2.4
.2
 DAV/
2
 SVN/
1.4
.2
X
-Powered-By: PHP/
5.2
.6

Access-Control-Allow-Origin: http://foo
.example

Access-Control-Allow-Credentials: true
Cache-Control: no-cache

Pragma:
 no-cache

Set
-Cookie: pageAccess=
3
; expires=Wed, 31-Dec-2008 01:34:53 GMT
Vary:
 Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 
106

Keep-Alive: timeout=
2
, max=
100
Connection:
 Keep-Alive
Content-Type: text/plain


[text/plain payload]


```

虽然第11行指定了要提交到[http://bar.other](http://bar.other/)的内容的Cookie信息,但是如果bar.other的响应头里没有Access-Control-Allow-Credentials:true\(第19行\),则响应会被忽略.

**特别注意: 给一个带有withCredentials的请求发送响应的时候,服务器端必须指定允许请求的域名,不能使用’\*’.**

上面这个例子中,如果响应头是这样的:Access-Control-Allow-Origin: \* ,则响应会失败.

在这个例子里,因为Access-Control-Allow-Origin的值是[http://foo.example](http://foo.example/)这个指定的请求域名,所以客户端把带有凭证信息的内容被返回给了客户端. 另外注意第22行,更多的cookie信息也被创建了.

