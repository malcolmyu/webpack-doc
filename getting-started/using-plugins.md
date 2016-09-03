使用插件来给 webpack 的打包添加功能性的典型依赖。例如 [BellOnBundlerErrorPlugin](https://github.com/senotrusov/bell-on-bundler-error-plugin) 插件，将会提醒我们在打包构建过程中产生的错误。

## **内置插件**

通过在 webpack 配置中使用插件属性，我们可以在模块中引入内置插件。

```js
// webpack 应该存在于 node_modules 目录中，如果不存在则需要安装
var webpack = require("webpack");

module.exports = {
    plugins: [
        new webpack.ResolverPlugin([
            new webpack.ResolverPlugin.DirectoryDescriptionFilePlugin("bower.json", ["main"])
        ], ["normal", "loader"])
    ]
};
```

## **其他插件**

对于没有内置的插件，如果其发布在 npm 源上，可以直接安装；如果没有发布可以使用其他方式：

```
npm install component-webpack-plugin
```

可以使用如下方式使用：

```js
var ComponentPlugin = require("component-webpack-plugin");
module.exports = {
    plugins: [
        new ComponentPlugin()
    ]
}
```

通过 npm 安装第三方插件时建议使用这个工具：[webpack-load-plugins](https://www.npmjs.com/package/webpack-load-plugins)。

它会检查所有安装在依赖中的第三方插件，并且在你需要的时候进行懒加载。

## **参见**

* [插件列表](http://webpack.github.io/docs/list-of-plugins.html)

