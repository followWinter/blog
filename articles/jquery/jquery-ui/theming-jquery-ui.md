### 主题化 jQuery UI
所有的 jQuery UI 插件被设计为允许开发者将 UI 组件在感觉和视觉上融入他们的站点或者应用。每一个插件都是用 CSS 来做样式，包含两层的信息：标准的 jQuery UI CSS 框架和指定插件样式。

jQuery UI CSS 框架提供语义化表示的类去只是组件内元素的角色，比如头部、内容区域、或者点击区域。这可以应用于所有的组件，可点击的标签页、手风琴、或者按钮都有相同的`ui-state-default`类，用来表示它可以被点击。当一个用户鼠标移动到这些元素上的时候，类改变为`ui-state-hover`，点击的时候改变为`ui-state-active`。这些相同的类风格让所有的组件拥有类似角色的元素或交互状态保证看起来一样变得很简单。
CSS 框架样式被概括在一个独立的文件中，叫做`theme.css`，这个文件是 ThemeRoller 应用定义的。框架样式只包含影响样式和交互（主颜色，背景图片，图标），所以他们是“安全”的样式，不会被独立的插件影响。这分离意味着一个开发者可以通过修改`theme.css`中的颜色和图片来创建自定义的样式和交互，而未来的插件或者一个 bug 修复也可以不用修改就和这个主题工作的很好。
因为框架样式只覆盖外观和感觉，插件指定样式被分离。包含实现组件功能需要的所有的额外结构样式规则，比如尺寸，内边距，外边距，定位，和浮动。当下载 jQuery UI 时，可以从`jquery-ui.structure.css`。

我们鼓励所有的开发者创建 jQuery 创建利用 jQuery UI CSS 框架，因为这可以让最终用户去主题化和使用你的插件更加简单。
### 开始
有三种常用的方式去主题化 jQuery UI 插件：
- 下载一个 ThemeRoller 主题：使用 ThemeRoller 去生成和下载主题是构建一个主题最简单的方式。这个 app 将会创建一个新的`query-ui.theme.css`文件和一个图片文件夹，它宝航所有必须的背景图片和图标雪碧图，可以简单被拖进你的项目。这个方式是创建和维护的最简单的方式，但是自定义性限制于 ThemeRoller。
- 修改 CSS 文件：为了得到一些更多对于外观和感觉的控制，你可能会选择从一个默认主题（Smoothness）或者 ThemeRoller 生成的主题开始，并调整`jquery-ui.theme.css`文件或者其他任意独立插件样式。比如，你可以简单的修改所有按钮的圆角和其他剩下的 UI 组件不同，或者改变秃瓢雪碧图的位置到自定义的集合。随着范围的扩大，你甚至可以使用多个主题到一个单独的 UI。为了保证维护简单，推荐只修改`jquery-ui.theme.css`文件和图片。
- 编写完整的 CSS：为了完整的控制，每一个插件的 CSS 都可以重新编写，而不实用框架类或者插件定义的样式。如果想要的外观和感觉无法通过修改 CSS 来达到或者高自定义性的标签被使用。这种方式需要很深的 CSS 经验，还需要为未来的插件手动更新。