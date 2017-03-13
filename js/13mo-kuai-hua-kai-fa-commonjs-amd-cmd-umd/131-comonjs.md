# 13.1 ComonJS

---

CommonJS是服务器端模块的规范，Node.js采用了这个规范。

根据CommonJS规范，一个单独的文件就是一个模块。加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的exports对象。

例如：

```
// foobar.js
 
//私有变量
var test = 123;
 
//公有方法
function foobar () {
 
    this.foo = function () {
        // do someing ...
    }
    this.bar = function () {
        //do someing ...
    }
}
 
//exports对象上的方法和变量是公有的
var foobar = new foobar();
exports.foobar = foobar;
```

```
//require方法默认读取js文件，所以可以省略js后缀
var test = require('./boobar').foobar;
 
test.bar();
```



