现今的网站正在不断演进为 web 应用：

* JavaScript 的使用场景越来越广
* 现代浏览器提供了更广泛的接口
* 全页面加载的应用越来越少，取而代之的是更多的单页应用

这就导致了客户端里有**太多**代码啦！

庞大的代码库需要被优化，模块系统提供了一种将代码分割为模块的方式。

# **模块系统类型**

---

我们有多种标准来规范如何定义依赖和导出值：

* `<script>` - 标签形式（无模块系统）
* CommonJS
* AMD 和一些方言
* ES6 模块化
* 其他方式……

## `<script>`** - 标签形式**

在不使用模块系统的时候，这就是我们操作模块化代码的方式。

```js
<script src="module1.js"></script>
<script src="module2.js"></script>
<script src="libraryA.js"></script>
<script src="module3.js"></script>
```

模块给全局对象（也就是 `window` 对象）导出接口，其他模块可以通过依赖全局对象来访问对应接口。

#### **常见问题**

* 全局对象冲突
* 加载顺序十分重要
* 开发者需要手动解决模块和库的依赖
* 在大项目中这一坨标签会变得冗长且难以管理

## **CommonJs：同步的 **`require`

这个方式使用同步的 `require` 方法以加载依赖并返回导出的接口。一个模块可以通过给 `exports` 对象添加属性或者设置 `module.exports` 的值的方式指定导出的值。

```js
require("module");
require("../file.js");
exports.doStuff = function() {};
module.exports = someValue;
```

服务端的 [node.js](http://nodejs.org/) 就使用了这种方式。

#### **优点**

* 服务端模块可被重用
* （在 npm 上）已经有许多模块都是用这种方式编写的
* 十分简洁，便于使用

#### **缺点**

* 阻塞调用在网络环境下表现不佳，网络请求是异步的
* 无法进行多模块并行获取

#### **实现**

* [node.js](http://nodejs.org/) - 服务端
* [browserify](https://github.com/substack/node-browserify)
* [modules-webmake](https://github.com/medikoo/modules-webmake) - 编译成一个 bundle 文件
* [wreq](https://github.com/substack/wreq) - 客户端

## **AMD：异步 **`require`

**异步模块定义**

其他的模块系统（浏览器环境下）使用同步的 `require` （CommonJS） 存在问题，于是便引入了一个异步的版本（以及一种定义模块和导出值的方式）：

```js
require(["module", "../file"], function(module, file) { /* ... */ });
define("mymodule", ["dep1", "dep2"], function(d1, d2) {
  return someExportedValue;
});
```

#### **优点**

* 适合网络环境下异步请求的方式
* 可以并行加载多个模块

#### **缺点**

* 更难读写，编写代码产生了额外开销
* 看起来像是某种解决方法而非系统

#### **实现**

* [require.js](http://requirejs.org/) - 客户端
* [curl](https://github.com/cujojs/curl) - 客户端

阅读更多关于 [CommonJS](http://webpack.github.io/docs/commonjs.html) 和 [AMD](http://webpack.github.io/docs/amd.html) 的内容。

## **ES6 模块系统**

EcmaScript6 给 JavaScript 添加了一些新的语言结构，构成了另一种模块系统。

```js
import "jquery";
export function doStuff() {}
module "localModule" {}
```

#### **优点**

* 易于静态分析
* 作为 ES 标准符合未来发展方向

#### **缺点**

* 浏览器的原生支持尚需时日
* 鲜有基于这种类型实现的模块

## **无偏解决方案**

让开发者自行选择模块类型，允许现存代码和依赖包正常工作，并方便添加自定义的模块类型。

# **传输**

---

模块需要在客户端执行，因此需要将其从服务端传输到浏览器。

有两种极端的传输模块方式：

* 一个模块一个请求
* 一个请求获取所有模块

这两种方式都被广泛使用，但都不甚理想：

* 一个模块一个请求

  * 优点：只有需要的模块才被传输
  * 缺点：更多的请求意味着更大的开销
  * 缺点：请求延迟导致的应用启动缓慢

* 一个请求获取所有模块

  * 优点：更少的请求开销，更少的延迟
  * 缺点：加载了还未被 `require` 的模块


## **分块传输**

更加灵活的传输方式效果更好，介于两种极端之间的方案更适合大多数情况。

→ 在编译所有模块的同时，将模块的集合分割成多个小批次（chunks）。

分块可以被多个小请求快速传输。分块的模块可以按需加载而不必初始引入，这就加速了应用的初始加载顺序，并且可获取更多的应用实际使用的代码。

『分割点』由开发者指定。

→ 庞大代码库的加载成为了可能！

注意：这个想法来源于谷歌的 [GWT](https://developers.google.com/web-toolkit/doc/latest/DevGuideCodeSplitting)。

阅读更多关于 [代码分割](http://webpack.github.io/docs/code-splitting.html)。

# **为什么只有 JavaScript**

---


为什么模块化系统只帮助开发者处理 JavaScript 呢？明明还有那么多资源需要处理：

* 样式表
* 图片
* web 字体
* html 模板
* 等等……

或者编译/预处理：

* coffeescript → javascript
* elm → javascript
* less 样式表 → css 样式表
* jade 模板 → javascript 生成 html
* i18n 文件 → 特定文件
* 等等……

这些资源应该被这样简单的使用：

```js
require("./style.css");
```

```js
require("./style.less");
require("./template.jade");
require("./image.png");
```

阅读更多关于 [加载器使用](http://webpack.github.io/docs/using-loaders.html) 和 [加载器](http://webpack.github.io/docs/loaders.html).

# **静态分析**

---

当编译所有模块的时候，静态分析尝试找出模块的依赖。

通常来说这种分析只能找到没有表达式的简单方式，但是 `require("./template/" + templateName + ".jade")` 却是我们引入依赖时常见的写法。

许多库都是用不同的方式编写，它们之中有些的写法异常诡异……

## **策略**

一个聪明的解析器应该允许大多数现存代码的运行。如果开发者用了什么黑魔法，它也应当尝试找到兼容方案。

