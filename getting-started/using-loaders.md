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

尽管不是强制要求，但按照管理加载器通常被命名为 `XXX-loader`，`XXX` 是上下文名称。例如 `json-loader`。

我们可以通过其全名（实际名称，如 `json-loader`）也可以通过缩写（如 `json`）来引用加载器。

The loader name convention and precedence search order is defined by [`resolveLoader.moduleTemplates`](http://webpack.github.io/docs/configuration.html#resolveloader-moduletemplates) within the webpack configuration API.

Loader name conventions may be useful, especially when referencing them within `require()` statements; see usage below.

## **Installing loaders**

If the loader is available on npm you can install the loader via:

```
$ npm install xxx-loader --save
```

or

```
$ npm install xxx-loader --save-dev
```

# **USAGE**

There are multiple ways to use loaders in your app:

* explicit in the `require` statement
* configured via configuration
* configured via CLI

## **loaders in **`require`

> _**Note:**__ Avoid using this, if at all possible, if you intend your scripts to be environment agnostic \(node.js and browser\). Use the configuration convention for specifying loaders \(see next section\)._

It’s possible to specify the loaders in the `require` statement \(or `define`, `require.ensure`, etc.\). Just separate loaders from resource with `!`. Each part is resolved relative to the current directory.

It’s possible to overwrite any loaders in the configuration by prefixing the entire rule with `!`.

```
require("./loader!./dir/file.txt");
// => uses the file "loader.js" in the current directory to transform
//    "file.txt" in the folder "dir".

require("jade!./template.jade");
// => uses the "jade-loader" (that is installed from npm to "node_modules")
//    to transform the file "template.jade"
//    If configuration has some transforms bound to the file, they will still be applied.

require("!style!css!less!bootstrap/less/bootstrap.less");
// => the file "bootstrap.less" in the folder "less" in the "bootstrap"
//    module (that is installed from github to "node_modules") is
//    transformed by the "less-loader". The result is transformed by the
//    "css-loader" and then by the "style-loader".
//    If configuration has some transforms bound to the file, they will not be applied.
```

## [**Configuration**](http://webpack.github.io/docs/configuration.html)

You can bind loaders to a RegExp via configuration:

```
{
    module: {
        loaders: [
            { test: /\.jade$/, loader: "jade" },
            // => "jade" loader is used for ".jade" files

            { test: /\.css$/, loader: "style!css" },
            // => "style" and "css" loader is used for ".css" files
            // Alternative syntax:
            { test: /\.css$/, loaders: ["style", "css"] },
        ]
    }
}
```

## [**CLI**](http://webpack.github.io/docs/cli.html)

You can bind loaders to an extension via CLI:

```
$ webpack --module-bind jade --module-bind 'css=style!css'
```

This uses the loader “jade” for “.jade” files and the loaders “style” and “css” for “.css” files.

## **Query parameters**

Loader can be passed query parameters via a query string \(just like in the web\). The query string is appended to the loader with `?`. i.e. `url-loader?mimetype=image/png`.

Note: The format of the query string is up to the loader. See format in the loader documentation. Most loaders accept parameters in the normal query format \(`?key=value&key2=value2`\) and as JSON object \(`?{"key":"value","key2":"value2"}`\).

### **in **`require`

```
require("url-loader?mimetype=image/png!./file.png");
```

### **Configuration**

```
{ test: /\.png$/, loader: "url-loader?mimetype=image/png" }
```

or

```
{
    test: /\.png$/,
    loader: "url-loader",
    query: { mimetype: "image/png" }
}
```

### **CLI**

```
webpack --module-bind "png=url-loader?mimetype=image/png"
```

