## **六、关于meta viewport的更多知识**

---

**1、关于缩放以及initial-scale的默认值**

    首先我们先来讨论一下缩放的问题，前面已经提到过，缩放是相对于ideal viewport来缩放的，缩放值越大，当前viewport的宽度就会越小，反之亦然。例如在iphone中，ideal viewport的宽度是320px，如果我们设置 initial-scale=2 ，此时viewport的宽度会变为只有160px了，这也好理解，放大了一倍嘛，就是原来1px的东西变成2px了，但是1px变为2px并不是把原来的320px变为640px了，而是在实际宽度不变的情况下，1px变得跟原来的2px的长度一样了，所以放大2倍后原来需要320px才能填满的宽度现在只需要160px就做到了。因此，我们可以得出一个公式：

> visual viewport宽度=idea viewport宽度/当前缩放值
>
> 当前缩放值=idea viewport宽度/visual viewport宽度

ps: visual viewport的宽度指的是浏览器可视区域的宽度。

    大多数浏览器都符合这个理论，但是安卓上的原生浏览器以及IE有些问题。安卓自带的webkit浏览器只有在 initial-scale = 1 以及没有设置width属性时才是表现正常的，也就相当于这理论在它身上基本没用；而IE则根本不甩initial-scale这个属性，无论你给他设置什么，initial-scale表现出来的效果永远是1。

    好了，现在再来说下initial-scale的**默认值问题**，就是不写这个属性的时候，它的默认值会是多少呢？很显然不会是1，因为当 initial-scale = 1 时，当前的layout viewport宽度会被设为 ideal viewport的宽度，但前面说了，各浏览器默认的 layout viewport宽度一般都是980啊，1024啊，800啊等等这些个值，没有一开始就是 ideal viewport的宽度的，所以 initial-scale的默认值肯定不是1。安卓设备上的initial-scale默认值好像没有方法能够得到，或者就是干脆它就没有默认值，一定要你显示的写出来这个东西才会起作用，我们不管它了，这里我们重点说一下iphone和ipad上的initial-scale默认值。

   根据测试，我们可以在iphone和ipad上得到一个结论，就是无论你给layout viewpor设置的宽度是多少，而又没有指定初始的缩放值的话，那么iphone和ipad会自动计算initial-scale这个值，以保证当前layout viewport的宽度在缩放后就是浏览器可视区域的宽度，也就是说不会出现横向滚动条。比如说，在iphone上，我们不设置任何的viewport meta标签，此时layout viewport的宽度为980px，但我们可以看到浏览器并没有出现横向滚动条，浏览器默认的把页面缩小了。根据上面的公式，**当前缩放值 = ideal viewport宽度  / visual viewport宽度，我们可以得出：**

      当前缩放值 = 320 / 980

也就是当前的initial-scale默认值应该是 0.33这样子。当你指定了initial-scale的值后，这个默认值就不起作用了。

总之记住这个结论就行了：**在iphone和ipad上，无论你给viewport设的宽的是多少，如果没有指定默认的缩放值，则iphone和ipad会自动计算这个缩放值，以达到当前页面不会出现横向滚动条\(或者说viewport的宽度就是屏幕的宽度\)的目的。**



## **2、动态改变meta viewport标签**

第一种方法

可以使用document.write来动态输出meta viewport标签，例如：

```
document.write('<meta name="viewport" content="width=device-width,initial-scale=1">')
```

第二种方法

通过setAttribute来改变 

```
<meta id="testViewport" name="viewport" content="width = 380">
<script>
var mvp = document.getElementById('testViewport');
mvp.setAttribute('content','width=480');
</script>
```

安卓2.3自带浏览器上的一个bug

```
<meta name="viewport" content="width=device-width">

<script type="text/javascript">
alert(document.documentElement.clientWidth); //弹出600，正常情况应该弹出320
</script>

<meta name="viewport" content="width=600">

<script type="text/javascript">
alert(document.documentElement.clientWidth); //弹出320，正常情况应该弹出600
</script>
```

---

参考 ：https://developer.mozilla.org/zh-CN/docs/Mobile/Viewport\_meta\_tag



