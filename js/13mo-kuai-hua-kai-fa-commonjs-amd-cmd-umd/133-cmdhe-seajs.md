# 13.3 CMD和seaJS

---

CMD是SeaJS 在推广过程中对模块定义的规范化产出

* 对于依赖的模块AMD是提前执行，CMD是延迟执行。不过RequireJS从2.0开始，也改成可以延迟执行（根据写法不同，处理方式不通过）。

* CMD推崇依赖就近，AMD推崇依赖前置。

```
//AMD
define(['./a','./b'], function (a, b) {
 
    //依赖一开始就写好
    a.test();
    b.test();
});
 
//CMD
define(function (requie, exports, module) {
     
    //依赖可以就近书写
    var a = require('./a');
    a.test();
     
    ...
    //软依赖
    if (status) {
     
        var b = requie('./b');
        b.test();
    }
});
```

虽然 AMD也支持CMD写法，但依赖前置是官方文档的默认模块定义写法。

* AMD的**API默认是一个当多个用，CMD严格的区分推崇职责单一**。例如：AMD里**require分全局的和局部的。CMD里面没有全局的 require，提供 seajs.use\(\)来实现模块系统的加载启动**。CMD里每个API都简单纯粹。



