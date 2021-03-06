### 特性和浏览器嗅探
### 我可以使用这个浏览器特性吗？
有一对常用的方式可以去检测用户的浏览器是否支持特定的特性：
- 浏览器嗅探
- 特殊特性嗅探
通常情况下，我们推荐特殊特性嗅探。让我们看看为什么。
### 浏览器嗅探
浏览器是一个检测浏览器用户代理（UA）字符串的方法，用来检测浏览器家族或者版本是否符合某个特殊的模式。比如，`Chrome 39`的`UA`字符串在`MAC OS X Yosemite`如下：

```
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.99 Safari/537.36
```
浏览器`UA`嗅探可能检测这个字符串像`Chrome`或者`Chrome/39`或者其他开发者觉得是他们目标浏览器的标示。

尽管这看起来是一个简单的解决方案，但是有一系列的问题：
- 其他不在你目标浏览器内的浏览器可能也有相同的问题。
如果我们指定不同的浏览器有不同的功能，意味着我们将没有统计到的浏览器排除了。这不是面向未来的。如果我们的目标浏览器接受了一个`bug fix`或者`change`，我们无法从`UA`中区别“可以工作”和“无法工作”。我们可能需要为每个新发布去更新我们的测试。这不是一个可维护的解决方案。

- 用户代理是不可靠的
用户代理是客户端浏览器设置的。在`web`早期，浏览器会互相模仿其他`UA`字符串，为了避免这种嗅探。具有不同功能的浏览器可能只模仿你的目标`UA`字符串。

`UA`字符串支持用户配置。尽管用户可能改变这个字符串，浏览器依旧支持这些特性。

### 指定特性嗅探
指定特性嗅探检测一个特定的特性是否可用，而不是为指定浏览器开发。通过这种方式，开发者可以写出两种场景的代码：支持提到的特性的浏览器，或者不支持提到的特性的浏览器。
为指定特性开发，而不是为指定浏览器开发，不仅仅让你的应用没有无关紧要的逻辑，也然你作为开发者的工作更简单。
我们推荐指定特性嗅探超过`UA`字符串嗅探。
现在你将会怎么做？
- 该怎么实现特性嗅探
有一系列的方式去实现特性嗅探：
- 直接`JavaScript`
- 一个帮助库
### 直接使用`JavaScript`
看看如何不使用帮助库来检测一个特殊的浏览器是否存在`<canvas>`元素。我们实现这个需求通过检测是否某个特殊的方法或者属性存在：
```
// We want to show a graph in browsers that support canvas,
// but a data table in browsers that don't.
var elem = document.createElement( "canvas" );
 
if ( elem.getContext && elem.getContext( "2d" ) ) {
    showGraph();
} else {
    showTable();
}
```
这是一个提供条件化体验的简单方式，取决于当前用户浏览器是否支持。我们可以提取出来作为一个帮助函数来重用，但是还是需要为每个我们关心的特性写一个测试。这是非常耗时并且易错的。所以你可能想要一个帮助库。

### 一个帮助库
谢天谢地，有一些非常棒的帮助库（比如[Modernizr]()）提供简单的，高级别的`API`来决定一个历览器是否支持执行的特性。
比如，使用`Modernizr`，我们可以使用以下代码足欧一样的`canvas`嗅探测试：
1
2
3
4
5
```
if ( Modernizr.canvas ) {
    showGraphWithCanvas();
} else {
    showTable();
}
```
了解更多关于`Modernizr`的信息，查阅他们的[文档]()。


### 性能考虑
所以，尽管`Modernizr`语法很棒，但是因为几个条件将会变得很麻烦。其次，我们发送了所有的选择给所有的浏览器，不管我们是否需要。

如果我们使用`Modernizr`，我们我们鼓励你使用[build configurator]()，允许你创建自定义构建的库。你可以把你不需要的检测去掉，这将会减少空间，并节约页面加载的事件。运行`Modernizr`的每一个检测，就算你不需要，会降低页面加载速度。

### 其他资源
### 特性嗅探工具
- [modernizr]()-条件检测浏览器是否支持指定的特性
- [htmlplease]()-负责任的使用新的和好的
- [html5please API]()-你可以查询一个指定的特性支持的程度的`API`
- [caniuse]()-`HTML5`、`CSS3`、`SVG`等技术的浏览器兼容表
### 有帮助的文章
Browser Feature Detection
- [浏览器特性嗅探]()
- [使用 Modernozr 去嗅探 HTML5 和 CSS3 浏览器的支持]()
- [填充 HTML5 的沟壑]()-Addy Osmani
- [特性，浏览器，和外形检测：这对环境很好]()-Michael Mahemoff