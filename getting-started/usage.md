# **安装**

你可以通过 npm 来安装 webpack：

```
npm install webpack -g
```

> _**注意:**_ _我们为了演示而全局安装了 webpack，当你创建一个真正的应用时，更明智的方式是将 webpack 作为项目的 __`devDependency`（开发依赖）。__ _

# **开始**

首先，我们将通过使用命令行接口来学习一下 webpack 的基本用法。

## **创建一个模块化 JavaScript 项目**

让我们使用 CommonJs 的语法创建一些 JavaScript 的模块：

**cats.js**

```js
var cats = ['dave', 'henry', 'martha'];
module.exports = cats;
```

**app.js（入口点）**

```js
cats = require('./cats.js');
console.log(cats);
```

『入口点』就是应用启动之处, 也是 webpack 开始在模块间追踪依赖之处。

## **5 秒钟学会 webpack**

给 webpack 提供入口点（app.js）并制定一个输出文件（app.bundle.js）：

```
webpack ./app.js app.bundle.js
```

webpack 将读取并分析入口点及其依赖（包括传递依赖），然后将其全部打包到 `app.bundle.js` 中。

![](https://dtinth.github.io/webpack-docs-images/usage/how-it-works.png)

现在我们的打包文件已可以准备运行了，运行 `node app.bundle.js` 并为我们拥有这么多猫而感到惊叹吧（**译者注：**文件运行的结果是输出了三只猫的名字）。

```js
node app.bundle.js
["dave", "henry", "martha"]
```

你可可以在浏览器环境中使用打包后的文件。

# **请注意**

webpack 是一个非常易于扩展的模块打包工具，他提供了很多先进的特性，但不是所有特性都暴露了命令行接口。为了更灵活的使用 webpack 的全部特性，我们需要创建一个『配置文件』。

## **项目结构**

在一个真实的 webpack 项目中，我们会通过在不同文件夹中管理资源文件与打包文件来将二者分开。例如，我们会将资源文件放到 **src** 中，并将打包文件放到 **bin** 中。

我们最终的项目结构可能看起来是这个样子：

![](https://raw.githubusercontent.com/dtinth/webpack-docs-images/2459637650502958669ea6b11bf49dc0b3b083ae/usage/project-structure.png)


> _在实际使用中存在太多的项目结构一些项目使用 __`app`__ 而非 __`src`__。一些项目使用 __`dist`__ 或 __`build`__ 来替代 __`bin`__。拥有自动化测试的项目通常使用 __`test`__、__`tests`__、__`spec`__、__`specs`__ 或者把测试文件也放在源代码文件夹中。_

1. 创建 `bin` 和 `src` 目录。

  ```
   mkdir bin
   mkdir src
  ```

2. 将原来的源代码文件移动端 `src` 文件夹。

  ```
   mv app.js cats.js src
  ```

3. 初始化 npm 项目。

  ```
   npm init #（回答命令行中的问题）
  ```

4. 将 webpack 作为开发依赖安装，这使你的项目可以声明其兼容的 webpack 版本。

  ```
   npm install --save-dev webpack
  ```


## **下面看下配置文件**

随着你项目的增长，你的配置将会变得复杂，使用命令行配置 webpack 会变得极为不便。让我们创建一个配置文件来代替这一工作。

1. 创建 `webpack.config.js`：

  ```js
   module.exports = {
       entry: './src/app.js',
       output: {
           path: './bin',
           filename: 'app.bundle.js'
       }
   };
  ```

  > _webpack 配置文件是 CommonJS 类型的模块。因此你完全可以在里面运行任意类型的代码，只要这个模块能导出一个配置对象即可。_


2. 只要写好了配置文件，我们就可以像这样简单的运行 webpack：

  ```
   webpack
  ```

  > _webpack 将读取配置文件，构建打包文件，并将其保存为 __`bin/app.bundle.js`__。如果检查 webpack 的输出文件，你会发现两个源文件都已包含在内。_

3. 运行 `bin/app.bundle.js`，我们会再次看到猫儿们。

  ```js
   node bin/app.bundle.js
   ["dave", "henry", "martha"]
  ```


> _我们也可以 __`require()`__ 那些通过 npm 安装的模块而无需其他配置_

## **使用加载器**

webpack 仅支持原生的 JavaScript 模块，但许多人会使用 ES2015、CoffeeScript、TypeScript 等编译工具。他们可以通过 [加载器](https://webpack.github.io/docs/using-loaders.html "Using Loaders")在 webpack 中使用。

加载器是一种特殊模块，webpack 使用它『加载』其他语言编写的模块转换成 webpack 能理解的 JavaScript 模块，例如 [`babel-loader`](https://github.com/babel/babel-loader) 使用 Babel 来加载用 ES2015 编写的文件。

[`json-loader`](https://github.com/webpack/json-loader) 加载 json 文件（就是简单地添加一个 `module.exports =`，然后将其导出为 CommonJs 的模块）。

加载器可被链接使用，有时我们必须链接使用加载器。例如，[`yaml-loader`](https://github.com/okonet/yaml-loader) 仅能将 YAML 文件转化为 JSON 文件，因此我们需要将其与 `json-loader` 链接方可使用。

### **使用 `babel-loader` 编译 ES2015**

在这个示例中，我们将告知 webpack 通过 [Babel](https://babeljs.io/) 运行我们的源文件，这样我们就可以使用 ES2015 中的新特性。

1. 安装 Babel 和预设（presets）：

  ```
   npm install --save-dev babel-core babel-preset-es2015
  ```

2. 安装 `babel-loader`：

  ```
   npm install --save-dev babel-loader
  ```

3. 通过添加 `.babelrc` 的方式，使用预设值配置 Babel：

  ```js
   { "presets": [ "es2015" ] }
  ```

4. 调整 `webpack.config.js` 文件，使其使用 `babel-loader` 处理所有的 `.js` 文件：

  ```js
   module.exports = {
       entry: './src/app.js',
       output: {
           path: './bin',
           filename: 'app.bundle.js',
       },
       module: {
           loaders: [{
               test: /\.js$/,
               exclude: /node_modules/,
               loader: 'babel-loader',
           }]
       }
   }
  ```

  > _我们在这里排除了 __`node_modules`__ 的加载，否则所有的外部依赖库都需要使用 Babel 编译，这样会减慢编译速度。_

5. 安装你想使用的库（例子中使用 jQuery）：

  ```
   npm install --save jquery babel-polyfill
  ```

  > _我们在这里使用 __`--save`__ 而不是 __`--save-dev`__，是因为这些库将会在运行时使用。我们也使用了 __`babel-polyfill`__ 以便在老浏览器中兼容 ES2015 的 API。_

6. 编辑 `src/app.js`：

  ```js
   import 'babel-polyfill';
   import cats from './cats';
   import $ from 'jquery';

   $('<h1>Cats</h1>').appendTo('body');
   const ul = $('<ul></ul>').appendTo('body');
   for (const cat of cats) {
       $('<li></li>').text(cat).appendTo(ul);
   }
  ```

7. 使用 webpack 打包模块

  ```
   webpack
  ```

8. 添加 `index.html` 使这个应用可以在浏览器中运行：

  ```html
   <!DOCTYPE html>
   <html>
       <head>
           <meta charset="utf-8">
       </head>
       <body>
           <script src="bin/app.bundle.js" charset="utf-8"></script>
       </body>
   </html>
  ```


当你打开 `index.html`，你应该就能看到一群猫啦！

我们还有很多[各种各样的加载器](https://webpack.github.io/docs/list-of-loaders.html "List of Loaders")，包括 css 和图片加载器，你可以用它们将各种文件引入到你的打包应用中。

## **使用插件**

通常情况下你还会想在你的工作流中给打包添加额外的处理流程。例如我们应该压缩文件，似的客户端可以更快加载。我们可以使用 [插件](https://webpack.github.io/docs/using-plugins.html "Using Plugins") 来完成这一工作。我们将在配置文件中添加 uglify 插件：

```js
const webpack = require('webpack');

module.exports = {
    entry: './src/app.js',
    output: {
        path: './bin',
        filename: 'app.bundle.js',
    },
    module: {
        loaders: [{
            test: /\.jsx?$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
        }]
    },
    plugins: [
        new webpack.optimize.UglifyJsPlugin({
            compress: {
                warnings: false,
            },
            output: {
                comments: false,
            },
        }),
    ]
}
```

webpack 中包含 Uglify 插件，因此我们不必添加额外的模块，但插件也不都是默认包含的。我们也可以编写 [自定义插件](https://webpack.github.io/docs/how-to-write-a-plugin.html "How to write a Plugin")。在这次构建中，uglify 插件将打包大小从 1618 字节压缩到 308 字节。

# **深入阅读**

* 查看 [CLI](http://webpack.github.io/docs/cli.html) 以了解命令行接口。
* 查看 [node.js API](http://webpack.github.io/docs/node.js-api.html) 以了解 node.js 的相关接口。
* 查看 [配置](http://webpack.github.io/docs/configuration.html) 来了解配置选项。

