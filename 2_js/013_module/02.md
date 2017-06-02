# 13.2 AMD和RequireJS

---

首先AMD模式，所谓异步模块加载。  
就是先加载数组里面得模块，然后等到加载完毕，才执行匿名函数。

随着RequireJS成为最流行的实现方式，异步模块规范（AMD）在前端界已经被广泛认同。  
它采用异步方式加载模块，**模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。**

AMD设计出一个简洁的写模块API：  
**define\(id?, dependencies?, factory\);**  
第一个参数 id 为字符串类型，表示了模块标识，为可选参数。若不存在则模块标识应该默认定义为在加载器中被请求脚本的标识。如果存在，那么模块标识必须为顶层的或者一个绝对的标识。  
第二个参数，dependencies ，是一个当前模块依赖的，已被模块定义的模块标识的数组字面量。  
第三个参数，factory，是一个需要进行实例化的函数或者一个对象。

通过参数的排列组合，这个简单的API可以从容应对各种各样的应用场景，如下所述。

* 定义无依赖的模块

```
define( {
    add : function( x, y ){
        return x + y ;
    }
} );
```

* 定义有依赖的模块

```
define(["alpha"], function( alpha ){
    return {
        verb : function(){
            return alpha.verb() + 1 ;
        }
    }
});
```

* 定义数据对象模块

```
define({
    users: [],
    members: []
});
```

* 具名模块

```
define("alpha", [ "require", "exports", "beta" ], function( require, exports, beta ){
    export.verb = function(){
        return beta.verb();
        // or:
        return require("beta").verb();
    }
});
```

* 包装模块

```
define(function(require, exports, module) {
    var a = require('a'),
          b = require('b');

    exports.action = function() {};
} );
```

不考虑多了一层函数外，格式和Node.js是一样的：使用require获取依赖模块，使用exports导出API。

除了define外，AMD还保留一个关键字require。require 作为规范保留的全局标识符，可以实现为 module loader，也可以不实现。

### 模块加载 {#articleHeader4}

require\(\[module\], callback\)

AMD模块化规范中使用全局或局部的require函数实现加载一个或多个模块，所有模块加载完成之后的回调函数。

其中：

\[module\]：是一个数组，里面的成员就是要加载的模块；callback：是模块加载完成之后的回调函数。

例如：加载一个math模块，然后调用方法 math.add\(2, 3\);

```
require(['math'], function(math) {
　math.add(2, 3);
});
```

## RequireJS

equireJS 是一个前端的模块化管理的工具库，遵循AMD规范，它的作者就是AMD规范的创始人 James Burke。所以说RequireJS是对AMD规范的阐述一点也不为过。

RequireJS 的基本思想为：通过一个函数来将所有所需要的或者说所依赖的模块实现装载进来，然后返回一个新的函数（模块），我们所有的关于新模块的业务代码都在这个函数内部操作，其内部也可无限制的使用已经加载进来的以来的模块。

### Require引入

```
<script data-main='scripts/main' src='scripts/require.js'></script>
```

那么scripts下的main.js则是指定的主代码脚本文件，所有的依赖模块代码文件都将从该文件开始异步加载进入执行。

### require定义模块

define用于定义模块，RequireJS要求每个模块均放在独立的文件之中。按照是否有依赖其他模块的情况分为独立模块和非独立模块。

* 独立模块，不依赖其他模块。直接定义：

```
define({
    method1: function(){},
    method2: function(){}
});
```

非独立模块，对其他模块有依赖。

```
define([ 'module1', 'module2' ], function(m1, m2) {
    ...
});
```

### require方法调用模块

在require进行调用模块时，其参数与define类似。

```
require(['foo', 'bar'], function(foo, bar) {
    foo.func();
    bar.func();
} );
```

在加载 foo 与 bar 两个模块之后**执行回调函数实现具体过程**。

当然还可以如之前的例子中的，在define定义模块内部进行require调用模块

define 和 require 这两个定义模块，调用模块的方法合称为AMD模式，定义模块清晰，不会污染全局变量，清楚的显示依赖关系。AMD模式可以用于浏览器环境并且允许非同步加载模块，也可以按需动态加载模块。

---

参考：https://segmentfault.com/a/1190000004873947

官网 \([http://www.requirejs.org/\)](http://www.requirejs.org/%29)

API \([http://www.requirejs.org/docs/api.html\)](http://www.requirejs.org/docs/api.html%29)

