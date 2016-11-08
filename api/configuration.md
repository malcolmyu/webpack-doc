> webpack 可传入一个配置对象，根据你对 webpack 的使用方式，有两种方式来传入该配置对象：

### 命令行接口

如果你使用[命令行接口](/)传入配置，其将会读取一个名为 `webpack.config.js` 的文件（或者通过 `--config` 选项传入的文件）。该文件将会导出配置对象：

``` javascript
module.exports = {
	// configuration
};
```

### node.js 接口

如果你使用 node.js 接口，你需要将配置对象通过参数传入：

``` javascript
webpack({
	// 配置
}, callback);
```

### 多个配置对象

在两种情况下，你都可以传入一组配置，它们会并行运行。它们将共享文件系统的缓存和监听器，因此比多次调用 webpack 更有效率。


# 配置对象内容

> 小提示：请注意你不必再配置项中写纯 JSON 语法。使用任何你想使用的 JavaScript 写法吧，这就是个 node.js 的模块……

一个非常简单的配置对象案例：

``` javascript
{
	context: __dirname + "/app",
	entry: "./entry",
	output: {
		path: __dirname + "/dist",
		filename: "bundle.js"
	}
}
```

## `context`

解析 `entry` 配置项的基础路径（是绝对路径哦！）。如果设置了 `output.pathinfo`，那么引入的路径信息将缩短到这一目录下。

> 默认值： `process.cwd()`

## `entry`

打包文件的入口点。

如果传入一个字符串：这个字符串会被解析为在启动时加载的一个模块。

如果传入一个数组：所有的模块都会在启动时被加载，最后一个模块会被导出。

``` javascript
entry: ["./entry1", "./entry2"]
```

如果传入一个对象，会有多个打包被创建，对象的 key 是 chunk 的名称，value 应为字符串或数组：

``` javascript
{
	entry: {
		page1: "./page1",
		page2: ["./entry1", "./entry2"]
	},
	output: {
		// 在使用多入口点时，确保在 output.filename 中使用 [name] 或 [id]
		filename: "[name].bundle.js",
		chunkFilename: "[id].bundle.js"
	}
}
```

> **注意**：对于特定的入口点不能再配置其他的选项了。如果你想对入口点进行具体的配置，需要使用 [多个配置对象](#multiple-configurations)。

## `output`

该配置项影响编译的输出。`output` 选项告知 webpack 如何将编译后的文件写入到硬盘上。注意，尽管可能存在多个 `entry` 入口点，但只应该有一个特定的 `output` 配置。

如果你使用任何哈希参数（`[hash]` 或 `[chunkhash]`），应使用 `OccurrenceOrderPlugin` 或 `recordsPath` 来确保模块顺序的一致性。

### `output.filename`

制定硬盘上每个输出文件的名称。你**不应该**在这里指定一个绝对路径！`output.path` 配置项决定了文件写入磁盘的位置。`filename` 仅用于命名各个文件。

**单个入口**
``` javascript
{
  entry: './src/app.js',
  output: {
    filename: 'bundle.js',
    path: __dirname + '/build'
  }
}

// 将文件写入到： ./build/bundle.js
```

**多个入口**

如果你的配置项创建了多个 chunk（如配置了多个入口点，或使用诸如 CommonsChunkPlugin 的插件），你应该使用关键字替换以确保每个文件拥有唯一的名称。

`[name]` 会被替换为 chunk 的名称。

`[hash]` 会被替换为编译生成的哈希值。

`[chunkhash]` 会被替换为 chunk 的哈希值。

``` javascript
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/build'
  }
}

// 将以下文件写入到磁盘中: ./build/app.js、./build/search.js
```

### `output.path`

输入目录为**绝对路径**（必填）。

`[hash]` 会被替换为编译生成的哈希值。

### `output.publicPath`

`publicPath` 指定了在浏览器引用输出文件时公共 URL 地址。对于内嵌了 `<script>` 或 `<link>` 标签或引入图片资源的加载器，当其文件路径与在磁盘中的路径（被 `path` 参数指定）不一致时，`publicPath` 将被用作指向文件的 `href` 或 `url()`。当你想对以部分或全部输出文件使用不同域名或使用 CDN 时，这一选项将很有帮助。webpack 开发者服务也使用这一配置项来确定服务中输出文件的预期路径。与 `path` 一样，你可以使用 `[hash]` 关键字替代以获得更好的缓存性能。

**config.js**

``` javascript
output: {
	path: "/home/proj/public/assets",
	publicPath: "/assets/"
}
```

**index.html**
``` html
<head>
  <link href="/assets/spinner.gif"/>
</head>
```

再来一个使用 CDN 和资源哈希值的复制例子：

**config.js**

``` javascript
output: {
	path: "/home/proj/cdn/assets/[hash]",
	publicPath: "http://cdn.example.com/assets/[hash]/"
}
```

**Note:** In cases when the eventual `publicPath` of output files isn't known at compile time, it can be left blank and set dynamically at runtime in the entry point file. If you don't know the `publicPath` while compiling, you can omit it and set `__webpack_public_path__` on your entry point.

**注意**：当编译时不知道最终输出文件的 `publicPath` 时，这一项也可以留空，并在运行时有入口点文件动态添加。如果在编译时不知道 `publicPath`，你可以省略它并在入口点设置 `__webpack_public_path__`。

``` javascript
 __webpack_public_path__ = myRuntimePublicPath

// 应用入口的余下内容
```

### `output.chunkFilename`

非入口 chunk 的文件名，是 `output.path` 目录下的相对路径。

`[id]` 会被替换为 chunk 的 id。

`[name]` 会被替换为 chunk 的名称（当没有名称时替换为 id）。

`[hash]` 会被替换为编译生成的哈希值。

`[chunkhash]` 会被替换为 chunk 的哈希值。

### `output.sourceMapFilename`

JavaScript 文件的 SoourceMaps 的文件名，位于 `output.path` 目录下。

`[file]` 会被替换为 JavaScript 文件的文件名。

`[id]` 会被替换为 chunk 的 id。

`[hash]` 会被替换为编译生成的哈希值。

> 默认值：`"[file].map"`

### `output.devtoolModuleFilenameTemplate`

给生成的 SourceMap 中的 `sources` 数组提供函数的文件名字符串模板（**译者注**：SourceMap 文件中的 `sources` 数组指的是转换前的文件名，可以参考阮一峰的[这篇文章](http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html)）。

`[resource]` 被替换为 webpack 解析文件使用的路径，包含最后侧加载器的查询参数（如果有查询参数的话）。

`[resource-path]` 与 `[resource]` 相同，但没有查询参数。

`[loaders]` 是加载器的列表，且其参数至多到最右侧的加载器（仅包括显式加载器）。

`[all-loaders]` 是加载器的列表，且其参数至多到最右侧的加载器（包括自动的加载器）。

`[id]` 被替换为模块的 id。

`[hash]` 被替换为模块标识的哈希值。

`[absolute-resource-path]` 被替换为文件名绝对路径。

> 默认 (devtool=`[inline-]source-map`)：`"webpack:///[resource-path]"`  
> 默认 (devtool=`eval`)：`"webpack:///[resource-path]?[loaders]"`  
> 默认 (devtool=`eval-source-map`)：`"webpack:///[resource-path]?[hash]"`

也可以被定义为函数而非模板字符串。

函数接受 `info` 参数，暴露如下属性：

- identifier
- shortIdentifier
- resource
- resourcePath
- absoluteResourcePath
- allLoaders
- query
- moduleId
- hash

### `output.devtoolFallbackModuleFilenameTemplate`

与 `output.devtoolModuleFilenameTemplate` 相似，应用于有模块标识符副本的场景。

> 默认值：`"webpack:///[resourcePath]?[hash]"`

### `output.devtoolLineToLine`

对指定模块启动行对行映射模式。行对行映射模式使用一个简单的 SourceMap，生成资源的每一行都映射到原始资源的同一行上。这是一个性能优化，只有当你的性能必须优化且需要保证源文件与生成文件的行一一对应的情况下可以使用这一方式。

设置为 `true` 会为所有模块开启（并不推荐）。

设置为与 `module.loaders` 类似的一个对象 `{test, include, exclude}` 会为指定文件开启。

> 默认值： disabled

### `output.hotUpdateChunkFilename`

热更新 chunks 的文件名，文件放置于 `output.path` 的目录下。

`[id]` 会被替换为 chunk 的 id。

`[hash]` 会被替换为编译生成的哈希值（最后的哈希值会被存储记录）。

> 默认值：`"[id].[hash].hot-update.js"`

### `output.hotUpdateMainFilename`

热更新主文件的文件名，文件放置于 `output.path` 的目录下。

`[hash]` 会被替换为编译生成的哈希值（最后的哈希值会被存储记录）。

> 默认值：`"[hash].hot-update.json"`

### `output.jsonpFunction`

webpack 用于异步加载 chunks 的 JSONP 函数。

一个短一点的函数会将文件大小减少一点。当在一个页面中拥有多个 webpack 实例时应使用不同的标识符。

> 默认值：`"webpackJsonp"`

### `output.hotUpdateFunction`

webpack 用于异步加载人更新 chunks 的 JSONP 函数。

> 默认值：`"webpackHotUpdate"`

### `output.pathinfo`

包含关于模块信息的注释。

`require(/* ./test */23)`

不要在生产环境下使用这一选项。

> 默认值：`false`

### `output.library`

如果设置了这一项，将会把打包文件导出为库，`output.library` 就是库名。

如果你正在编写一个库并想把它导出成单个文件时使用。

### `output.libraryTarget`

指定导出为什么格式的库：

`"var"` - 通过设定变量导出：`var Library = xxx`（默认）

`"this"` - 通过设定为 `this` 的属性导出：`this["Library"] = xxx`

`"commonjs"` - 通过设定为 `exports` 的属性导出：`exports["Library"] = xxx`

`"commonjs2"` - 通过设定为 `module.exports` 导出：`module.exports = xxx`

`"amd"` - 导出为 AMD 格式（可选参数名称，通过 `library` 选项设置名称）

`"umd"` - 导出为 AMD、CommonJS2 或全局属性

> 默认值：`"var"`

如果未设置 `output.library`，但 `output.libraryTarget` 设置为不是 `var` 的值，会将导出对象的所有属性进行复制（除非设置为 `amd`、`commonjs2` 和 `umd`）。

### `output.umdNamedDefine`

如果 `output.libraryTarget` 设置为 `umd` 且设置了 `output.library`，将该选项设置为 `true` 会为 AMD 模块命名。

### `output.sourcePrefix`

为打包文件的每一行添加对应的字符串前缀。

> Default: `"\t"`

### `output.crossOriginLoading`

该选项允许跨域加载 chunks。

可用的值有：

`false` - 禁止跨域加载。

`"anonymous"` - 启用跨域请求，使用 `anonymous` 在发送请求时不会携带凭证。

`"use-credentials"` - 启用跨域请求，且在发送请求时携带凭证。

关于跨域加载的更多信息，参见 [MDN](https://developer.mozilla.org/en/docs/Web/HTML/Element/script#attr-crossorigin)

> 默认值：`false`



## `module`

Options affecting the normal modules (`NormalModuleFactory`)

### `module.loaders`

An array of automatically applied loaders.

Each item can have these properties:

* `test`: A condition that must be met
* `exclude`: A condition that must not be met
* `include`: An array of paths or files where the imported files will be transformed by the loader
* `loader`: A string of "!" separated loaders
* `loaders`: An array of loaders as string

A condition may be a `RegExp` (tested against absolute path), a `string` containing the absolute path, a `function(absPath): bool`, or an array of one of these combined with "and".

See more: [[loaders]]

*IMPORTANT*: The loaders here are resolved *relative to the resource* which they are applied to. This means they are not resolved relative to the configuration file. If you have loaders installed from npm and your `node_modules` folder is not in a parent folder of all source files, webpack cannot find the loader. You need to add the `node_modules` folder as an absolute path to the `resolveLoader.root` option. (`resolveLoader: { root: path.join(__dirname, "node_modules") }`)

Example:

``` javascript
module: {
  loaders: [
    {
      // "test" is commonly used to match the file extension
      test: /\.jsx$/,

      // "include" is commonly used to match the directories
      include: [
        path.resolve(__dirname, "app/src"),
        path.resolve(__dirname, "app/test")
      ],

      // "exclude" should be used to exclude exceptions
      // try to prefer "include" when possible

      // the "loader"
      loader: "babel-loader" // or "babel" because webpack adds the '-loader' automatically
    }
  ]
}
```

### `module.preLoaders`, `module.postLoaders`

Syntax like `module.loaders`.

An array of applied pre and post loaders.

### `module.noParse`

Don't parse files matching a RegExp or an array of RegExps.

It's matched against the full resolved request.

This can boost the performance when ignoring big libraries.

The files are expected to have no call to `require`, `define`, or similar. They are allowed to use `exports` and `module.exports`.

### automatically created contexts defaults `module.xxxContextXxx`

There are multiple options to configure the defaults for an automatically created context. We differentiate three types of automatically created contexts:

* `exprContext`: An expression as a dependency (i. e. `require(expr)`)
* `wrappedContext`: An expression plus pre- and/or suffixed string (i. e. `require("./templates/" + expr)`)
* `unknownContext`: Any other unparsable usage of `require` (i. e. `require`)

Four options are possible for automatically created contexts:

* `request`: The request for context.
* `recursive`: Subdirectories should be traversed.
* `regExp`: The RegExp for the expression.
* `critical`: This type of dependency should be consider as critical (emits a warning).

All options and defaults:

`unknownContextRequest = "."`, `unknownContextRecursive = true`, `unknownContextRegExp = /^\.\/.*$/`, `unknownContextCritical = true`

`exprContextRequest = "."`, `exprContextRegExp = /^\.\/.*$/`, `exprContextRecursive = true`, `exprContextCritical = true`

`wrappedContextRegExp = /.*/`, `wrappedContextRecursive = true`, `wrappedContextCritical = false`

> Note: `module.wrappedContextRegExp` only refers to the middle part of the full RegExp. The remaining is generated from prefix and suffix.

Example:

``` javascript
{
  module: {
	// Disable handling of unknown requires
	unknownContextRegExp: /$^/,
	unknownContextCritical: false,

	// Disable handling of requires with a single expression
	exprContextRegExp: /$^/,
	exprContextCritical: false,

	// Warn for every expression in require
	wrappedContextCritical: true
  }
}
```



## `resolve`

Options affecting the resolving of modules.

### `resolve.alias`

Replace modules with other modules or paths.

Expects an object with keys being module names. The value is the new path. It's similar to a replace but a bit more clever. If the key ends with `$` only the exact match (without the `$`) will be replaced.

If the value is a relative path it will be relative to the file containing the require.

Examples: Calling a require from `/abc/entry.js` with different alias settings.

| `alias:` | `require("xyz")` | `require("xyz/file.js")` |
|---|---|---|
| `{}` | `/abc/node_modules/xyz/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "/absolute/path/to/file.js" }` | `/absolute/path/to/file.js` | error |
| `{ xyz$: "/absolute/path/to/file.js" }` | `/absolute/path/to/file.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "./dir/file.js" }` | `/abc/dir/file.js` | error |
| `{ xyz$: "./dir/file.js" }` | `/abc/dir/file.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "/some/dir" }` | `/some/dir/index.js` | `/some/dir/file.js` |
| `{ xyz$: "/some/dir" }` | `/some/dir/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "./dir" }` | `/abc/dir/index.js` | `/abc/dir/file.js` |
| `{ xyz: "modu" }` | `/abc/node_modules/modu/index.js` | `/abc/node_modules/modu/file.js` |
| `{ xyz$: "modu" }` | `/abc/node_modules/modu/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "modu/some/file.js" }` | `/abc/node_modules/modu/some/file.js` | error |
| `{ xyz: "modu/dir" }` | `/abc/node_modules/modu/dir/index.js` | `/abc/node_modules/dir/file.js` |
| `{ xyz: "xyz/dir" }` | `/abc/node_modules/xyz/dir/index.js` | `/abc/node_modules/xyz/dir/file.js` |
| `{ xyz$: "xyz/dir" }` | `/abc/node_modules/xyz/dir/index.js` | `/abc/node_modules/xyz/file.js` |

`index.js` may resolve to another file if defined in the `package.json`.

`/abc/node_modules` may resolve in `/node_modules` too.

### `resolve.root`

The directory (**absolute path**) that contains your modules. May also be an array of directories. This setting should be used to add individual directories to the search path.

> It **must** be an **absolute path**! Don't pass something like `./app/modules`.

Example:

``` javascript
var path = require('path');

// ...
resolve: {
  root: [
    path.resolve('./app/modules'),
    path.resolve('./vendor/modules')
  ]
}
```

### `resolve.modulesDirectories`

An array of directory names to be resolved to the current directory as well as its ancestors, and searched for modules. This functions similarly to how node finds "node_modules" directories. For example, if the value is `["mydir"]`, webpack will look in "./mydir", "../mydir", "../../mydir", etc.

> Default: `["web_modules", "node_modules"]`

> Note: Passing `"../someDir"`, `"app"`, `"."` or an absolute path isn't necessary here. Just use a directory name, not a path. Use only if you expect to have a hierarchy within these folders. Otherwise you may want to use the `resolve.root` option instead.

### `resolve.fallback`

A directory (or array of directories **absolute paths**), in which webpack should look for modules that weren't found in `resolve.root` or `resolve.modulesDirectories`.

### `resolve.extensions`

An array of extensions that should be used to resolve modules. For example, in order to discover CoffeeScript files, your array should contain the string `".coffee"`.

> Default: `["", ".webpack.js", ".web.js", ".js"]`

**IMPORTANT**: Setting this option will override the default, meaning that webpack will no longer try to resolve modules using the default extensions. If you want modules that were required with their extension (e.g. `require('./somefile.ext')`) to be properly resolved, you **must** include an empty string in your array. Similarly, if you want modules that were required without extensions (e.g. `require('underscore')`) to be resolved to files with ".js" extensions, you **must** include `".js"` in your array.

### `resolve.packageMains`

Check these fields in the `package.json` for suitable files.

> Default: `["webpack", "browser", "web", "browserify", ["jam", "main"], "main"]`

**Note**: This option has been changed to `resolve.mainFields` in webpack 2.

### `resolve.packageAlias`

Check this field in the `package.json` for an object. Key-value-pairs are treated as aliasing according to [this spec](https://github.com/defunctzombie/package-browser-field-spec)

> Not set by default

Example: `"browser"` to check the browser field.

### `resolve.unsafeCache`

Enable aggressive but unsafe caching for the resolving of a part of your files. Changes to cached paths may cause failure (in rare cases). An array of RegExps, only a RegExp or `true` (all files) is expected. If the resolved path matches, it'll be cached.

> Default: `[]`



## `resolveLoader`

Like `resolve` but for loaders.

``` javascript
// Default:
{
	modulesDirectories: ["web_loaders", "web_modules", "node_loaders", "node_modules"],
	extensions: ["", ".webpack-loader.js", ".web-loader.js", ".loader.js", ".js"],
	packageMains: ["webpackLoader", "webLoader", "loader", "main"]
}
```

Note that you can use `alias` here and other features familiar from `resolve`. For example `{ txt: 'raw-loader' }` would shim `txt!templates/demo.txt` to use `raw-loader`.

### `resolveLoader.moduleTemplates`

That's a `resolveLoader` only property.

It describes alternatives for the module name that are tried.

> Default: `["*-webpack-loader", "*-web-loader", "*-loader", "*"]`



## `externals`

Specify dependencies that shouldn't be resolved by webpack, but should become dependencies of the resulting bundle. The kind of the dependency depends on `output.libraryTarget`.

As value an object, a string, a function, a RegExp, and an array is accepted.

* string: An exact matched dependency becomes external. The same string is used as external dependency.
* object: If an dependency matches exactly a property of the object, the property value is used as dependency. The property value may contain a dependency type prefixed and separated with a space. If the property value is `true` the property name is used instead. If the property value is `false` the externals test is aborted and the dependency is not external. See example below.
* function: `function(context, request, callback(err, result))` The function is called on each dependency. If a result is passed to the callback function this value is handled like a property value of an object (above bullet point).
* RegExp: Every matched dependency becomes external. The matched text is used as the `request` for the external dependency.  Because the `request` _is the exact code_ used to generate the external code hook, if you are matching a commonjs package (e.g. '../some/package.js'), instead use the function external strategy. You can import the package via `callback(null, "require('" + request + "')"`, which generates a `module.exports = require('../some/package.js');`, using require outside of webpack context.
* array: Multiple values of the scheme (recursive).

Example:

``` javascript
{
	output: { libraryTarget: "commonjs" },
	externals: [
		{
			a: false, // a is not external
			b: true, // b is external (require("b"))
			"./c": "c", // "./c" is external (require("c"))
			"./d": "var d" // "./d" is external (d)
		},
		// Every non-relative module is external
		// abc -> require("abc")
		/^[a-z\-0-9]+$/,
		function(context, request, callback) {
			// Every module prefixed with "global-" becomes external
			// "global-abc" -> abc
			if(/^global-/.test(request))
				return callback(null, "var " + request.substr(7));
			callback();
		},
		"./e" // "./e" is external (require("./e"))
	]
}
```

| type        | value               | resulting import code |
|-------------|---------------------|-----------------------|
| "var"       | `"abc"`             | `module.exports = abc;` |
| "var"       | `"abc.def"`         | `module.exports = abc.def;` |
| "this"      | `"abc"`             | `(function() { module.exports = this["abc"]; }());` |
| "this"      | `["abc", "def"]`    | `(function() { module.exports = this["abc"]["def"]; }());` |
| "commonjs"  | `"abc"`             | `module.exports = require("abc");` |
| "commonjs"  | `["abc", "def"]`    | `module.exports = require("abc").def;` |
| "amd"       | `"abc"`             | `define(["abc"], function(X) { module.exports = X; })` |
| "umd"       | `"abc"`             | everything above |

Enforcing `amd` or `umd` in a external value will break if not compiling as amd/umd target.

> Note: If using `umd` you can specify an object as external value with property `commonjs`, `commonjs2`, `amd` and `root` to set different values for each import kind.




## `target`

* `"web"` Compile for usage in a browser-like environment (default)
* `"webworker"` Compile as WebWorker
* `"node"` Compile for usage in a node.js-like environment (use `require` to load chunks)
* `"async-node"` Compile for usage in a node.js-like environment (use `fs` and `vm` to load chunks async)
* `"node-webkit"` Compile for usage in webkit, uses jsonp chunk loading but also supports build in node.js modules plus require("nw.gui") (experimental)
* `"electron"` Compile for usage in [Electron](http://electron.atom.io/) – supports `require`-ing Electron-specific modules.
* `"electron-renderer"` Compile for electron renderer process, provide a target using `JsonpTemplatePlugin`, `FunctionModulePlugin` for browser environment and `NodeTargetPlugin` and `ExternalsPlugin` for commonjs and electron bulit-in modules. *Note: need `webpack` >= 1.12.15.*



## `bail`

Report the first error as a hard error instead of tolerating it.



## `profile`

Capture timing information for each module.

> Hint: Use the [analyze tool](http://webpack.github.io/analyse) to visualize it. `--json` or `stats.toJson()` will give you the stats as JSON.



## `cache`

Cache generated modules and chunks to improve performance for multiple incremental builds.

This is enabled by default in watch mode.

You can pass `false` to disable it.

You can pass an object to enable it and let webpack use the passed object as cache. This way you can share the cache object between multiple compiler calls. Note: Don't share the cache between calls with different options.


## `debug`

Switch loaders to debug mode.



## `devtool`

Choose a developer tool to enhance debugging.

`eval` - Each module is executed with `eval` and `//@ sourceURL`.

`source-map` - A SourceMap is emitted. See also `output.sourceMapFilename`.

`hidden-source-map` - Same as `source-map`, but doesn't add a reference comment to the bundle.

`inline-source-map` - A SourceMap is added as DataUrl to the JavaScript file.

`eval-source-map` - Each module is executed with `eval` and a SourceMap is added as DataUrl to the `eval`.

`cheap-source-map` - A SourceMap without column-mappings. SourceMaps from loaders are not used.

`cheap-module-source-map` - A SourceMap without column-mappings. SourceMaps from loaders are simplified to a single mapping per line.

Prefixing `@`, `#` or `#@` will enforce a pragma style. (Defaults to `@` in `webpack@1` and `#` in `webpack@2`; using `#` is recommended)

Combinations are possible. `hidden`, `inline`, `eval` and pragma style are exclusive.

i. e. `cheap-module-inline-source-map`, `cheap-eval-source-map`, `#@source-map`

> Hint: If your modules already contain SourceMaps you'll need to use the [source-map-loader](https://github.com/webpack/source-map-loader) to merge it with the emitted SourceMap.

| devtool                      | build speed | rebuild speed | production supported | quality                 |
|------------------------------|-------------|---------------|----------------------|-------------------------|
| eval                         |     +++     |      +++      |       no       | generated code                |
| cheap-eval-source-map        |      +      |      ++       |       no       | transformed code (lines only) |
| cheap-source-map             |      +      |       o       |       yes      | transformed code (lines only) |
| cheap-module-eval-source-map |      o      |      ++       |       no       | original source (lines only)  |
| cheap-module-source-map      |      o      |       -       |       yes      | original source (lines only)  |
| eval-source-map              |     --      |       +       |       no       | original source               |
| source-map                   |     --      |       --      |       yes      | original source               |

Example:

``` javascript
{
	devtool: "#inline-source-map"
}
// =>
//# sourceMappingURL=...
```

> Note: With the next major version the default for `-d` will change to `cheap-module-eval-source-map`

## `devServer`

Can be used to configure the behaviour of [webpack-dev-server](https://github.com/webpack/webpack-dev-server) when the webpack config is passed to webpack-dev-server CLI.

Example:

``` javascript
{
	devServer: {
		contentBase: "./build",
	}
}
```

## `node`

Include polyfills or mocks for various node stuff:

* `console`: `true` or `false`
* `global`: `true` or `false`
* `process`: `true`, `"mock"` or `false`
* `Buffer`: `true` or `false`
* `__filename`: `true` (real filename relative to the context option), `"mock"` (`"/index.js"`) or `false` (normal node __dirname)
* `__dirname`: `true` (real dirname relative to the context option), `"mock"` (`"/"`) or `false` (normal node __dirname)
* `<node buildin>`: `true`, `"mock"`, `"empty"` or `false`


``` javascript
// Default:
{
	console: false,
	global: true,
	process: true,
	Buffer: true,
	__filename: "mock",
	__dirname: "mock",
	setImmediate: true
}
```


## `amd`

Set the value of `require.amd` and `define.amd`.

Example: `amd: { jQuery: true }` (for old 1.x AMD versions of jquery)



## `loader`

Custom values available in the loader context.



## `recordsPath`, `recordsInputPath`, `recordsOutputPath`

Store/Load compiler state from/to a json file. This will result in persistent IDs of modules and chunks.

An **absolute path** is expected. `recordsPath` is used for `recordsInputPath` and `recordsOutputPath` if they left undefined.

This is required when using Hot Code Replacement between multiple calls to the compiler.



## `plugins`

Add additional plugins to the compiler.
