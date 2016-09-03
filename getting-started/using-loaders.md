# **什么是加载器？**

加载器是应用于你应用的资源文件的转换器，它们是运行在 node.js 中的函数，以资源文件作为参数输入，并返回新的资源。

举例说明，你可以使用加载器来告诉 webpack 来加载 CoffeeScript 或 JSX。

## **加载器的特性**

* 加载器可被链式使用。它们以管道的方式应用到资源上，最后的加载器应返回 JavaScript；其他的加载器则可以返回任意格式的资源，并传递到下一个加载器中。
* 加载器可以是同步的，也可以是异步的。
* 加载器运行在 Node.js 中，且可以做 Node.js 能做的任何事情。
* 加载器接受参数，因此可以给加载器传递配置项。
* 在配置文件中，加载器可以绑定到对应的扩展名/正则表达式。
* 加载器可以通过 `npm` 发布和安装。
* 标准模块可以通过 `package.json` `loader`，在标准的 `main` 之外导出一个加载器。
* 加载器可以访问配置文件。
* 插件可以给加载器提供更多的特性。
* 加载器可以导出额外的任意文件。
* [等等](http://webpack.github.io/docs/loaders.html)。

如果你对一些加载器的例子感兴趣，可以查看[加载器列表](http://webpack.github.io/docs/list-of-loaders.html)。

# **解析加载器**

加载器的解析[与模块类似](http://webpack.github.io/docs/resolving.html)。一个加载器模块期望导出一个函数，并应该以与 node.js 兼容的模块编写。通常情况下我们通过 npm 管理加载器，但我们的加载器也可是是应用中的文件。

## **引用加载器**

尽管不是强制要求，但按照惯例加载器通常被命名为 `XXX-loader`，`XXX` 是上下文名称。例如 `json-loader`。

我们可以通过其全名（实际名称，如 `json-loader`）也可以通过缩写（如 `json`）来引用加载器。

加载器命名约定和搜索顺序优先级被 webpack 的配置 API [`resolveLoader.moduleTemplates`](http://webpack.github.io/docs/configuration.html#resolveloader-moduletemplates) 定义。

加载器命名约定十分有益，除非直接使用 `require()` 语句引用。可以参见下面的使用场景。

## **安装加载器**

如果加载器在 npm 上可用，你可以通过这样的方式安装：

```
$ npm install xxx-loader --save
```

或者

```
$ npm install xxx-loader --save-dev
```

# **使用**

在我们的应用中有多种方式使用加载器：

* 直接使用 `require` 语句
* 在配置文件中配置
* 通过命令行接口（CLI）配置

## **使用 `require` 引入**

> _**注意：**假如你打算让你的脚本运行在不确定的环境中（node.js 或浏览器），就要尽可能避免使用这种方式。对于指定的加载器使用配置文件的配置方式（参加下一节）。 _

It’s possible to specify the loaders in the `require` statement \(or `define`, `require.ensure`, etc.\). Just separate loaders from resource with `!`. Each part is resolved relative to the current directory.
我们可以在 `require` 语句（或者 `define`、`require.ensure` 等）中可以指定加载器，仅仅需要使用 `!` 将加载器和资源分开。每一部分的解析均相对于当前目录。

在配置文件中可以通过在整条规则之前添加 `!` 前缀的方式覆盖任意加载器。

```js
require("./loader!./dir/file.txt");
// => 在当前目录下使用 loader.js 来转换 dir 路径下的文件 file.txt 

require("jade!./template.jade");
// => 使用 jade-loader（该加载器通过 npm 安装在 node_modules 中）
//    来转换文件 template.jade
//    如果配置文件中有绑定到该文件的一些转换器，那这些转换器也会被应用。

require("!style!css!less!bootstrap/less/bootstrap.less");
// => 在 less 路径下的 bootstrap 模块中的文件 bootstrap.less
//    (该模块通过 github 安装到 node_modules) 被 less-loader 转换。
//    其结果被 css-loader 转换，然后再被 "style-loader" 转换。
//    如果配置文件中有绑定到该文件的一些转换器，那这些转换器也会被应用。
```

## [**配置文件方式**](http://webpack.github.io/docs/configuration.html)

你可以通过配置文件将加载器绑定到一个正则表达式上：

```js
{
    module: {
        loaders: [
            { test: /\.jade$/, loader: "jade" },
            // => jade 加载器应用于 .jade 文件

            { test: /\.css$/, loader: "style!css" },
            // => style、css 加载器应用于 .css 文件
            // 也可以使用下面这种写法：
            { test: /\.css$/, loaders: ["style", "css"] },
        ]
    }
}
```

## [**命令行接口方式**](http://webpack.github.io/docs/cli.html)

你可以通过命令行接口将加载器绑定到扩展名上：

```
$ webpack --module-bind jade --module-bind 'css=style!css'
```

这表示对 .jade 文件使用 jade 加载器，对 .css 文件使用 style 加载器和 css 加载器。

## **查询参数**

加载器通过一个查询字符串（正如 web 中的查询字符串一样）的方式传入查询参数。查询字符串添加到加载器后面，通过 `?` 分割，如：`url-loader?mimetype=image/png`。

注意：查询字符串的格式取决于加载器，可在加载器的文档中查询其格式。大多数加载器接受标准查询格式 `?key=value&key2=value2` 或 JSON 对象 `{"key":"value","key2":"value2"}`。

### **在 `require` 中使用 **

```js
require("url-loader?mimetype=image/png!./file.png");
```

### **配置文件中使用**

```js
{ test: /\.png$/, loader: "url-loader?mimetype=image/png" }
```

或

```js
{
    test: /\.png$/,
    loader: "url-loader",
    query: { mimetype: "image/png" }
}
```

### **命令行使用**

```
webpack --module-bind "png=url-loader?mimetype=image/png"
```

