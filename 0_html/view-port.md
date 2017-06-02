# view-port移动端开发（一）

---

## **一、viewport的概念**

通俗的讲，移动设备上的viewport就是设备的屏幕上能用来显示我们的网页的那一块区域，在具体一点，就是浏览器上\(也可能是一个app中的webview\)用来显示网页的那部分区域，但viewport又不局限于浏览器可视区域的大小，它可能比浏览器的可视区域要大，也可能比浏览器的可视区域要小。在默认情况下，一般来讲，移动设备上的viewport都是要大于浏览器可视区域的，这是因为考虑到移动设备的分辨率相对于桌面电脑来说都比较小，所以为了能在移动设备上正常显示那些传统的为桌面浏览器设计的网站，移动设备上的浏览器都会把自己默认的viewport设为980px或1024px（也可能是其它值，这个是由设备自己决定的），但带来的后果就是浏览器会出现横向滚动条，因为浏览器可视区域的宽度是比这个默认的viewport的宽度要小的。下图列出了一些设备上浏览器的默认viewport的宽度。

| iphone | ipad | android Samsung | android HTC | Chrome | Opera Presto | BlackBerrey | IE |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 980 | 980 | 980 | 980 | 980 | 980 | 1024 | 1024 |

## **二、css中的1px并不等于设备的1px**

CSS中的像素px实质上是一个抽象的概念，在不同的设备或者不同的环境中，css中1px代表的设备物理像素是不同的。

在早先的设备上，如iphone3，它的分辨率为320\*480，在iphone3上面，一个css像素确实是等于一个屏幕物理像素的。后来随着技术的发展，如iphone4，苹果推出了Retina屏，分辨率提高了一倍，变成了640\*960，但是屏幕尺寸没有变化。那么也就意味着，一个CSS像素等于2个物理像素了。其他的安卓机也是同样的道理。

还有一个因素也会引起css中px的变化，那就是用户缩放。例如，当用户把页面放大一倍，那么css中1px所代表的物理像素也会增加一倍；反之把页面缩小一倍，css中1px所代表的物理像素也会减少一倍。关于这点，在文章后面的部分还会讲到。

在移动端浏览器中以及某些桌面浏览器中，window对象有一个**devicePixelRatio属性**，它的官方的定义为：设备物理像素和设备独立像素的比例，也就是 devicePixelRatio = 物理像素 / 独立像素。css中的px就可以看做是设备的独立像素，所以通过devicePixelRatio，我们可以知道该设备上一个css像素代表多少个物理像素。例如，在Retina屏的iphone上，devicePixelRatio的值为2，也就是说1个css像素相当于2个物理像素。但是要注意的是，devicePixelRatio在不同的浏览器中还存在些许的兼容性问题，所以我们现在还并不能完全信赖这个东西，具体的情况可以看下[这篇文章](http://www.quirksmode.org/blog/archives/2012/06/devicepixelrati.html)。

### devicePixelRatio的测试结果：![](/assets/300958489622990.png)

### **三、PPK的关于三个viewport的理论**

ppk把移动设备上的viewport分为_**layout viewport**_ 、_**visual viewport  **_和_**ideal viewport**_三类，

其中的ideal viewport是最适合移动设备的viewport，ideal viewport的宽度等于移动设备的屏幕宽度，只要在css中把某一元素的宽度设为ideal viewport的宽度\(单位用px\)，那么这个元素的宽度就是设备屏幕的宽度了，也就是宽度为100%的效果。ideal viewport 的意义在于，无论在何种分辨率的屏幕下，那些针对ideal viewport 而设计的网站，不需要用户手动缩放，也不需要出现横向滚动条，都可以完美的呈现给用户

