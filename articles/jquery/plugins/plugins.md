### 插件
一个`jQuery`插件就是一个简单的新方法，我们用来扩展`jQuery`的原型对象。通过扩展原型对象，所有的`jQuery`对象都可以继承你添加的任何方法。如上所述，当你调用`jQuery()`的时候，你创建了一个新的`jQuery`对象，该对象继承`jQuery`所有的方法。

插件的用处就是用一组元素做一些事情。你可以认为`jQuery`核心的方法都是一个插件，比如`.fadeOut()`或者`.addClass()`。

你可以创建你的插件，在你的代码中作为私用，也可以发布他们公共的地方。在线上有成千上万的插件可以使用。制作插件的难度非常低，你可以直接去做。
- [查找和评估插件]()
- [怎样创建一个基本的插件]()
- [高级插件理念]()
- [使用`jQuery UI Widget Factory`编写有状态的插]()