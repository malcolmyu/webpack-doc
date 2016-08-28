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

Now your bundle is ready to be run. Run `node app.bundle.js` and marvel in your abundance of cats.
现在我们的打包文件已可以准备运行了，运行 `node app.bundle.js` 

```
node app.bundle.js
["dave", "henry", "martha"]
```

You can also use the bundle in the browser.

# **GETTING SERIOUS**

webpack is a very flexible module bundler. It offers a lot of advanced features, but not all features are exposed via the command-line interface. To gain full access to webpack’s flexibility, we need to create a “configuration file”.

## **Project structure**

In real-world webpack projects, we’ll separate the source files from the bundled files by organizing them in folders. For example, we’ll put the source files in **src**, and bundled files in **bin**.

Our final project structure will look like this:



> _In the wild, there are many project structures. Some projects use __`app`__ instead of __`src`__. Some projects use __`dist`__ or__`build`__ instead of __`bin`__. Projects with tests usually use __`test`__, __`tests`__, __`spec`__, __`specs`__ or colocate the test files in the source folder._

1. Create the `bin` and `src` directory.

  ```
   mkdir bin
   mkdir src
  ```

2. Move the original source files to the `src` folder.

  ```
   mv app.js cats.js src
  ```

3. Initialize an npm project.

  ```
   npm init # (answer the questions)
  ```

4. Install webpack as a development dependency. This lets your project declare the version of webpack it is compatible with.

  ```
   npm install --save-dev webpack
  ```


## **Moving to a configuration file.**

As your project grows and your configuration becomes more complex, it becomes unwieldy to configure webpack from the command line. Let’s create a configuration file instead.

1. Create `webpack.config.js`:

  ```
   module.exports = {
       entry: './src/app.js',
       output: {
           path: './bin',
           filename: 'app.bundle.js'
       }
   };
  ```

  > _A webpack configuration file is a CommonJS-style module. So you can run any kind of code here, as long as a configuration object is exported out of this module._

2. With the configuration file in place, you can now simply run webpack like this:

  ```
   webpack
  ```

  > _webpack will read the configuration file, build the bundle, and save it as __`bin/app.bundle.js`__. If you examine webpack’s output you’ll see that it included both source files._

3. Run `bin/app.bundle.js` and you’ll get your list of cats again.

  ```
   node bin/app.bundle.js
   ["dave", "henry", "martha"]
  ```


> _You can also __`require()`__ modules installed via npm with no extra configuration._

## **Using loaders**

webpack only supports JavaScript modules natively, but most people will be using a transpiler for ES2015, CoffeeScript, TypeScript, etc. They can be used in webpack by using [loaders](https://webpack.github.io/docs/using-loaders.html "Using Loaders").

Loaders are special modules webpack uses to ‘load’ other modules \(written in another language\) into JavaScript \(that webpack understands\). For example, [`babel-loader`](https://github.com/babel/babel-loader) uses Babel to load ES2015 files.



[`json-loader`](https://github.com/webpack/json-loader) loads JSON files \(simply by prepending `module.exports =` to turn it into a CommonJs module\).



Loaders can be chained, and sometimes you need to chain loaders together. For example, [`yaml-loader`](https://github.com/okonet/yaml-loader) only converts YAML into JSON. Therefore, you need to chain it with `json-loader` so that it can be used.



### **Transpiling ES2015 using **`babel-loader`

In this example, we’re going to tell webpack to run our source files through [Babel](https://babeljs.io/) so we can use ES2015 features.

1. Install Babel and the presets:

  ```
   npm install --save-dev babel-core babel-preset-es2015
  ```

2. Install `babel-loader`:

  ```
   npm install --save-dev babel-loader
  ```

3. Configure Babel to use these presets by adding `.babelrc`

  ```
   { "presets": [ "es2015" ] }
  ```

4. Modify `webpack.config.js` to process all `.js` files using `babel-loader`.

  ```
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

  > _We are excluding __`node_modules`__ here because otherwise all external libraries will also go through Babel, slowing down compilation._

5. Install the libraries you want to use \(in this example, jQuery\):

  ```
   npm install --save jquery babel-polyfill
  ```

  > _We are using __`--save`__ instead of __`--save-dev`__ this time, as these libraries will be used in runtime. We also use__`babel-polyfill`__ so that ES2015 APIs are available in older browsers._

6. Edit `src/app.js`:

  ```
   import 'babel-polyfill';
   import cats from './cats';
   import $ from 'jquery';

   $('<h1>Cats</h1>').appendTo('body');
   const ul = $('<ul></ul>').appendTo('body');
   for (const cat of cats) {
       $('<li></li>').text(cat).appendTo(ul);
   }
  ```

7. Bundle the modules using webpack:

  ```
   webpack
  ```

8. Add `index.html` so this app can be run in browser:

  ```
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


When you open `index.html`, you should now see a list of cats!



There are a number of [different loaders](https://webpack.github.io/docs/list-of-loaders.html "List of Loaders") you can use to include files in your app bundle, including css and image loaders.

## **Using plugins**

Usually you’ll want to do some additional processing of the bundle in your workflow. An example would be minifying your file so that clients can load it faster. This can be done with [plugins](https://webpack.github.io/docs/using-plugins.html "Using Plugins"). We’ll add the uglify plugin to our configuration:

```
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

The Uglify plugin is included with webpack so you don’t need to add additional modules, but this may not always be the case. You can write your own [custom plugins](https://webpack.github.io/docs/how-to-write-a-plugin.html "How to write a Plugin"). For this build, the uglify plugin cut the bundle size from 1618 bytes to 308 bytes.

# **FURTHER READING**

* see [CLI](http://webpack.github.io/docs/cli.html) for the command line interface.
* see [node.js API](http://webpack.github.io/docs/node.js-api.html) for the node.js interface.
* see [Configuration](http://webpack.github.io/docs/configuration.html) for the configuration options. ..
