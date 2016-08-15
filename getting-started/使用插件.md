Use plugins to add functionality typically related to bundles in webpack. For example, the[BellOnBundlerErrorPlugin](https://github.com/senotrusov/bell-on-bundler-error-plugin) will notify you of an error in the bundler build process.

## **Built-in plugins**

Plugins are included in your module by using the plugins property in the webpack config.

```
// webpack should be in the node_modules directory, install if not.
var webpack = require("webpack");

module.exports = {
    plugins: [
        new webpack.ResolverPlugin([
            new webpack.ResolverPlugin.DirectoryDescriptionFilePlugin("bower.json", ["main"])
        ], ["normal", "loader"])
    ]
};
```

## **Other plugins**

Plugins that are not built-in may be installed via npm if published there, or by other means if not:

```
npm install component-webpack-plugin
```

which can then be used as follows:

```
var ComponentPlugin = require("component-webpack-plugin");
module.exports = {
    plugins: [
        new ComponentPlugin()
    ]
}
```

When installing third party plugins via npm it is advised to use this tool: [https:\/\/www.npmjs.com\/package\/webpack-load-plugins](https://www.npmjs.com/package/webpack-load-plugins)

It checks for all third party plugins installed in your dependencies and lazyloads them when you need them.

## **See also**

* [list of plugins](http://webpack.github.io/docs/list-of-plugins.html)

