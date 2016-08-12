# 欢迎来到 Webpack 文档站

![](https://webpack.github.io/assets/what-is-webpack.png)

你可以在左侧边栏浏览文章内容。

如果你是第一次听说 webpack，你可能需要阅读本页面或左侧的『开始』章节。

## 插件

webpack 拥有丰富的插件接口，其众多特性就是内部插件使用这些接口实现。webpack 因此拥有很强的**扩展性**。

## 性能

webpack 使用异步 I\/O 并拥有多级缓存。这使得 webpack 能快速运行，且在增量编译上拥有难以置信的速度。

## 加载器（Loaders）

webpack 支持通过加载器对文件进行预处理。这一特性使我们不仅能打包 JavaScript 代码，且能打包任何**静态资源**。你可以轻松编写运行在 node.js 环境中的加载器。

## 支持

webpack 支持 **AMD 和 CommonJs** 的模块类型。他会对你代码的抽象语法树（AST）进行巧妙的分析。它甚至拥有一个评估简单表达式的评估引擎，这让我们可以使用大部分现有的代码库。

## 代码分割

webpack 允许我们将代码库分割为**按需**加载的代码块（chunks），由此可以减少初始加载时间。

## 优化

webpack 可以进行需要优化以**减少输出文件大小**，它也通过使用哈希的方式关注请求**缓存**。

## 开发工具

webpack 支持 SourceUrls 和 **SourceMaps **来进行简单的 debug。它可以监听你的文件变化，并通过开发中间件和开发服务器来进行**自动重载**。

## 多目标

webpack 的主要目标是**网页**，但也支持给 [WebWorkers](https://github.com/webpack/worker-loader) 和 node.js。

