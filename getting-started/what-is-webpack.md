**webpack** 是一个**模块化打包工具**。

webpack 获取带有依赖的模块，并通过这些模块生成静态资源。

![](http://webpack.github.io/assets/what-is-webpack.png)

## **为什么不选择其他的模块化打包工具？**

现有的模块化打包工具并不适用于大项目（大型单页应用）。开发另一个模块打包器的最紧迫的原因是[代码分割](http://webpack.github.io/docs/code-splitting.html)，同时其他静态资源应该通过模块化无缝集成在一起。

我尝试过扩展现有的模块打包器，却无法满足所有的需求。

## **目标**

* 将依赖树分割为 chunks 并按需加载
* 确保较低的初始加载时间
* 所有静态资源都可被作为模块
* 拥有能继承第三方库作为模块的能力
* 拥有能自定义模块打包器绝大多数功能的能力
* 适用于大项目

## **webpack 的不同之处？**

#### [**代码分割**](http://webpack.github.io/docs/code-splitting.html)

webpack 的依赖树中有两种类型的依赖：同步的和异步的。异步依赖作为分割点，形成一块新的分块。当分块树优化之后，每一个分块均会作为一个文件发送出去。

深入了解[代码分割](http://webpack.github.io/docs/code-splitting.html)。

#### [**加载器**](http://webpack.github.io/docs/loaders.html)
 
webpack 仅能处理原生 JavaScript，但加载器可以将其他资源转化为 JavaScript。
通过这样的处理，每一个资源会成为一个模块。

深入了解[加载器的使用](http://webpack.github.io/docs/using-loaders.html) and [加载器](http://webpack.github.io/docs/loaders.html)。

#### **智能解析**

webpack 有一个智能的解析器可以处理绝大多数第三方库。它甚至允许诸如 `require("./templates/" + name + ".jade")` 的表达式依赖。它可处理大多数常用的模块：[CommonJs](http://webpack.github.io/docs/commonjs.html) 和 [AMD](http://webpack.github.io/docs/amd.html)。

深入了解 [表达式依赖](http://webpack.github.io/docs/context.html)，[CommonJs](http://webpack.github.io/docs/commonjs.html) 和 [AMD](http://webpack.github.io/docs/amd.html)。

#### [**插件系统**](http://webpack.github.io/docs/plugins.html)

webpack 拥有丰富的插件系统，众多内部特性的实现也基于插件系统。这使得我们可以按照需求自定义 webpack 且开源通用的插件。

深入了解[插件](http://webpack.github.io/docs/plugins.html)。

