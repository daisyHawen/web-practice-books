# 1.requireJS模块化之循环依赖

如果你定义一个循环依赖关系** \(a 依赖b 并且 b 依赖 a\)**，那么当b的模块构造函数被调用的时候，传递给他的a会是undefined。 但是**b可以在a模块在被引入之后通过require\(‘a’\)来获取a** \(一定要把require作为依赖模块，RequireJS才会使用正确的 context 去查找 a\):

```js
//Inside b.js:
define(["require", "a"],
    function(require, a) {
        //"a" in this case will be null if a also asked for b,
        //a circular dependency.
        return function(title) {
            return require("a").doSomething();
        }
    }
);
```

通常情况下，你**不应该使用require\(\)的方式来获取一个模块**，而是使用传递给模块构造函数的参数。**循环依赖很罕见，通常表明，你可能要重新考虑这一设计。**然而，有时需要这样用，在这种情况下，就使用上面那种指定require\(\)的方式吧。

如果你熟悉 CommonJS 模块的写法，**你也可以使用 exports 创建一个空对象来导出模块，这样定义的模块可以被其他模块立即使用。即使在循环依赖中，也可以安全的直接使用**。 不过这只适用于**导出的模块是对象，而不是一个函数:**

```js
//Inside b.js:
define(function(require, exports, module) {
    //If "a" has used exports, then we have a real
    //object reference here. However, we cannot use
    //any of a's properties until after b returns a value.
    var a = require("a");

    exports.foo = function () {
        return a.bar();
    };
});
```

用依赖数组的话，记得将 ['exports'](https://github.com/jrburke/requirejs/wiki/Differences-between-the-simplified-CommonJS-wrapper-and-standard-AMD-define#wiki-magic)当作依赖模块：

```js
//Inside b.js:
define(['a', 'exports'], function(a, exports) {
    //If "a" has used exports, then we have a real
    //object reference here. However, we cannot use
    //any of a's properties until after b returns a value.

    exports.foo = function () {
        return a.bar();
    };
});
```



