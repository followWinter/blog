[原文地址](https://reactjs.org/docs/concurrent-mode-intro.html)
### Introducing Concurrent Mode(Experimental)

注意：

这个页面描述了**稳定发布中[还不可用](https://reactjs.org/docs/concurrent-mode-adoption.html)的实验性功能**。不要在生产应用中依赖 React 的试验性构建。在成为 React 的一部分之前，这些功能可能发生非常大的改变，并且没有警告。

这个文档面向早期接受者和好奇的人。**如果你刚接触 React，不要担心这些功能** -- 你不需要立马学习他们。

这个页面提供了一个 Concurrent Mode 的理论概述。**了解更多实际介绍，你应该要查阅下一个章节**：

- [Suspense for Data Fetching](https://reactjs.org/docs/concurrent-mode-suspense.html) 描述了一个在 React 组件获取数据的新的机制。

- [Concurrent UI patterns](https://reactjs.org/docs/concurrent-mode-patterns.html)显示了 Concurrent Mode 和 Suspense 可以做到的 UI 模式。

- [Adopting Concurrent Mode](https://reactjs.org/docs/concurrent-mode-adoption.html)解释怎样在你的项目尝试 Concurrent Mode。

- [Concurrent Mode API reference](https://reactjs.org/docs/concurrent-mode-reference.html)记录了试验性构建中可用的新的 API。

### 什么是 Concurrent 模式？

Concurrent 模式是一系列新功能，帮助 React 应用保持响应，并优雅的调整用户的设备能力和网络速度。

这些功能依旧是试验性的，并且处于改变中。他们还不属于 React 稳定发行的一部分，但是你可以在试验性构建中尝试他们。

### 堵塞和可中断的渲染

**为了解释 Concurrent 模式，我们将使用版本控制作为比喻**。如果你在一个团队工作，你可能使用版本控制系统，比如 Git，并在分支上工作。当一个分支准备好了，你可以合并你的工作到 master，这样其他人可以拉取它。

在版本控制存在之前，开发者工作流非常不同。没有分支的概念，如果你想要去编辑一些文件，你需要去告诉每个人不要去修改这些文件，直到你完成你的工作。你甚至无法和那个人并行开始工作 -- 你直接被他们堵塞了。

这描述了包括 React 在哪的 UI 库通常的工作方式。一旦他们开始渲染和更新，包括创建一个新的 DOM 节点并运行组件内部的代码，他们不能被中断。我们叫这种方式为"堵塞渲染"。

在 Concurrent 模式，渲染不是被堵塞的。它是可中断的。这提高了用户体验。它也解锁了之前不可能的新功能。在了解[下一章节](https://reactjs.org/docs/concurrent-mode-patterns.html)的实际例子之前，我们将对新功能做一个高级别的概览。

### 可中断的渲染

假设我们有一个可以过滤的产品列表。你曾经输入到一个列表过滤，并在每次输入都感觉卡顿吗？有一些更新产品列表的功能可能无法避免，比如创建新的 DOM 节点或者浏览器执行布局。然而，我们何时和怎样执行工作很重要。

解决这个卡顿的一种常见方式是让输入框"防抖"。当防抖的时候，我们只在用户停止输入的时候更新列表。然而，UI 在我们输入的时候不更新是让人很沮丧的。还有一个替代方案，我们可以让输入框"节流"，使用某个最大的频率更新列表。但是在低功率设备，我们依旧会遇到卡顿。防抖和节流都导致了不好的用户体验。

卡顿的原因很简单：一旦渲染开始，它不能被中断。因此浏览器不能在按下键盘的时候立即更新。不论基准测试看起来多好的 UI 库（比如 React），如果它使用堵塞渲染，你组件中的某些工作将总是导致卡顿。并且，通常，这不容易修复。


**Concurrent 模式通过让渲染可中断修复了这个基础限制**。 这意味着当用户按下其他按键，React 不需要堵塞浏览器更新文本输入框。相反，他可以让浏览器为输入框绘制更新，并继续在内存中渲染更新的列表。当渲染完成，React 更新 DOM，改变被反映在屏幕上。

你可以认为 React "在一个分支上"准备每一个更新。就像你可以放弃分支上的工作或者在他们之间切换，Concurrent 模式下的 React 可以中断一个正在进行的更新去做一些更重要的，然后回来继续做之前做的。这个技术可能让你想起视频游戏中的[双缓冲](https://wiki.osdev.org/Double_Buffering)。

Concurrent 模式技术减少 UI 对防抖和节流的需要。因为渲染是可中断的，React 不需要人工延迟工作去避免卡顿。他可以马上开始渲染，但是在需要保持应用响应的时候中断工作。


### Intentional Loading  Sequences

我们之前说过 Concurrent 模式就像 React 工作"在一个分支"。分支不仅对于短期修复很有用，对长期运行的功能也是。有时候你可能在一个功能上工作，但是在他变成"足够好的状态"去合并到 master 之前，可能需要很久。这一方面，我们版本控制比喻也可以应用于渲染。

想象我们在应用的两个屏幕之间导航。有时候我们可能没有足够的代码和加载的数据在屏幕上为用户显示一个"足够好"的加载状态。过渡到一个空屏幕或者一个巨大的旋转可能是一个非常差的体验。然而，通常必要的代码和数据不需要太长时间去获取。**如果 React 可以保持旧的屏幕更长一点时间，并在显示新屏幕之前"跳过"那"坏的加载状态"不是更好吗？**

尽管现在这可能，但是很难编排。在 Concurrent 模式，这个功能是内置的。React 首先在内存中准备新屏幕 -- 或者，就像我们比喻的，"在一个不同的分支"。因此，React 可以在更新 DOM 之前等待，这样更多的内容可以被加载。在 Concurrent 模式，我们可以告诉 React 去保持显示旧的屏幕，完全可交互的，带一个行内加载指示器。当新的屏幕准备好了，React 将会带我们到它。

### Concurrency

让我们回去前面的两个例子来看看 Concurrent 模式如何统一他们。**在 Concurrent 模式，React 可以同步进行多个状态更新** -- 就像分支让不同的团队成员独立工作：

- 对于 CPU 密集更新（比如创建 DOM 节点和运行组件代码），并发意味着更多紧急更新可以"中断"已经开始的渲染。

- 对于 IO 密集更新（比如获取代码或者从网络获取数据），并发意味着 React 可以在内存渲染，就算在数据到达之前，并跳过显示糟糕的加载状态。

重要的是，你使用 React 的方式是相同的。关于组件，属性，和状态的概念依旧以相同的方式工作。当你想要更新屏幕，你设置状态。

React 使用启发式去决定一个更新怎样"紧急"，让你使用一些代码调整它，这样你可以让每次交互达到期望的用户体验。

### Putting Research into Production

关于 Concurrent 模式有一个常见的主题。**它的目的是将人机交互研究的发现整合到真实 UI 中**。

比如，研究表明在屏幕之间过度的时候显示太多中间加载状态感觉更慢。这是为什么 Concurrent 模式在一个固定的"调度"显示一个新的加载状态去避免糟糕和太频繁的更新。

同样的，我们从研究知道，像 hover 和文本输入框交互需要在非常短的时间周期内处理，当点击和页面过度可以等待稍微长一点的时间不会感觉延迟。Concurrent 模内部为在人类感知眼周中大约对应不同的交互分类使用的不同的"优先级"。

强烈关注用户体验的团队有时候使用一次性的解决方案解决了类似的问题。然而，这些解决方案很少能长时间存活，因为他们很难维护。使用 Concurrent 模式，我们的目标是去转化 UI 搜索研究发现到抽象本身，并提供惯用的方式去使用他们。作为一个 UI 库，React 在这方面有利。

### 下一步
现在你知道 Concurrent 模式是关于什么的了！

在下一个页面，你将会学习更多关于指定话题的详请：

- [Suspense for Data Fetching](https://reactjs.org/docs/concurrent-mode-suspense.html) 描述了一个在 React 组件获取数据的新的机制。

- [Concurrent UI patterns](https://reactjs.org/docs/concurrent-mode-patterns.html)显示了 Concurrent Mode 和 Suspense 可以做到的 UI 模式。

- [Adopting Concurrent Mode](https://reactjs.org/docs/concurrent-mode-adoption.html)解释怎样在你的项目尝试 Concurrent Mode。

- [Concurrent Mode API reference](https://reactjs.org/docs/concurrent-mode-reference.html)记录了试验性构建中可用的新的 API。



















