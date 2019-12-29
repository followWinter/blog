### 立即执行函数
`jQuery`打包之后的代码不算多，就这个版本来看，`jquery.js`文件一共 10364 行，其中还包括空行、注释等。

### IIFE 原理
将所有的代码折叠起来之后，可以看到这么一个结构
```
( function( global, factory ) {
    ...
})(typeof window !== "undefined" ? window : this, function( window, noGlobal ){...})
```
最外层其实是两个连续的`()`，即`()()`，这形成了立即执行函数`IIFE`。
其中第一个`()`传入一个函数，该函数接受两个参数
- `global`
- `factory`

而第二个`()`传入了两个参数
- `typeof window !== "undefined" ? window : this`：一个变量
- `function(window, noGlobal){...}`：一个函数

// TODO: 函数声明和函数表达式的区别

// TODO: 表达式是啥


在第一个`()`传入的函数其实要注意不是`函数声明`，而是一个`函数表达式`，一般函数声明是
```
function A(){}
```
是带有函数名的，而函数表达式：
```
function(){}
```
是可以不带函数名的，甚至我们也叫它匿名函数。如果直接编写上面的代码到控制台，会触发一个错误：
```
Uncaught SyntaxError: Function statements require a function name
```
因为以`function`开头的语句会被解析为`函数声明`，但是`函数声明`是需要标识符的，也就是函数名。

而这里
```
(function( global, factory ){...})
```
使用`()`包裹，避免了这个错误。

同时`()`在`js`中可以返回内部表达式的的结果，而这里正好返回了一个函数。

第二个`()`则完成了对此函数的调用，并传入两个参数。

### 好处
`jQuery`的代码基本放在了第二个`()`的第二个参数中，从 41 行到 10364 行。

使用`IIFE`的好处是不会污染全局空间，将所有的变量和方法都约束在一个不可访问的函数内部。

其实使用函数就已经可以做到上面的好处，和`IIFE`无关，之所以要使用`IIFE`是因为我们在编写库的时候需要做一个初始化工作，我们希望用户在引入的时候就可以使用，那么，就可以使用`IIFE`直接完成初始化工作。

所以，在我看来`IIFE`是为了执行一次性的函数而使用的，而不是为了所谓了不污染全局空间。因为就算使用`IIFE`，想要让用户可以使用，也必须给一个引用给用户，比如`$`，而这，其实已经污染全局空间了，虽然只占用了一个，但是那就只能算最小化污染空间。那我使用一个函数也是可以的：
```
// 方式1
var libA = function (){ ... }
// 方式2
function libB(){ ... }
```
在这里，`jQuery`外层的`IIFE`并不是为了初始化`jQuery`而存在的，而是为了兼容各种环境，比如`Node`和浏览器。

### 参考资料

- https://zhuanlan.zhihu.com/p/27657358