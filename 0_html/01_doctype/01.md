# Doctype和浏览器渲染模式

---

DOCTYPE，或者称为 Document Type Declaration（文档类型声明，缩写 DTD）。

首先，DOCTYPE并不是可有可无的

## 浏览器模式

有三种解析方式:

* 非怪异（标准）模式
* 怪异模式
* 部分怪异（近乎标准）模式

1. 检测浏览器兼容模式document.compatMode

通过document.compatMode属性可以检测浏览器的模式，例如下面的这一行代码没有在头部声明&lt;!DOCTYPE html&gt;

```
<html>
<head></head>
<body>
<script>
document.write(document.compatMode);
</script>
</body>
</html>
```

那么在IE和Chrome浏览器里会输出：BackCompat；

如果加了&lt;!DOCTYPE html&gt;，浏览器就会输出：CSS1Compat

BackCompat：标准兼容模式（Standards-compliant mode）未开启；

CSS1Compat：标准兼容模式已开启。

从 document.compatMode 返回的字符串值中也可以看出来，BackCompat 代表了向后兼容（即 IE5.5），CSS1Compat 代表了对 CSS1 规范的兼容（即 IE6）。由此，浏览器的工作模式被分为了混杂模式及标准模式。

## DOCTYPE 的选择 {#header_3}

## HTML 5 {#1}

```
<!DOCTYPE html>
```

## HTML 4.01 Strict {#2}

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
```

该 DTD 包含所有[HTML](http://harttle.com/tags.html#HTML)元素和属性，但不包括展示性的和弃用的元素（比如 font）。不允许框架集（Framesets）。

## HTML 4.01 Transitional {#3}

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" 
"http://www.w3.org/TR/html4/loose.dtd">
```

该 DTD 包含所有[HTML](http://harttle.com/tags.html#HTML)元素和属性，包括展示性的和弃用的元素（比如 font）。不允许框架集（Framesets）。

### 工作模式简介

_浏览器的工作模式_常被称为“渲染模式”。实际上浏览器不同的工作模式不仅对渲染有影响，对代码的解析以及脚本的行为也同样有影响。

从更广泛的角度来看，浏览器的工作模式的差异不仅体现在处理 HTML 页面的时候，处理 XML 及一些非 WEB 内容时也有模式上的差异，

