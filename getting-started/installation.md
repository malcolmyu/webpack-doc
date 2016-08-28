## **node.js**

安装 [node.js](http://nodejs.org/)。

node.js 拥有一个叫做 `npm` 的包管理器。

## **webpack**

webpack 可以通过 `npm` 安装：

```
$ npm install webpack -g
```

webpack 现在被全局安装，且可以使用 `webpack` 命令了。

## **在项目中使用 webpack**

最好在项目中也将 webpack 作为依赖，这样之后我们就可以给当前的 webpack 选择一个版本，而不必强制使用那一个全局的 webpack。

使用 `npm` 添加一个 `package.json` 配置文件：

```
$ npm init
```

如果我们不想发布项目到 npm，那么之后控制台里的安装选项随便填一下就好了，并不重要。

安装 `webpack` 并将其加入 `package.json` ：

```
$ npm install webpack --save-dev
```

## **版本**

webpack 有两个可用版本：稳定版和 beta 版。beta 版本在其版本号里用 `-beta` 标记。beta 版本可能包含不稳定的改动、实验性的功能和较少的测试。可以通过[变更日志](http://webpack.github.io/docs/changelog.html) 来查看版本间的不同。 在正式的开发里你应该选择稳定的版本：

```
$ npm install webpack@1.2.x --save-dev
```

## **开发工具**

如果我们想使用开发工具，可以自行安装：

```
$ npm install webpack-dev-server --save-dev
```

## **深入了解**

你可以继续阅读[使用](http://webpack.github.io/docs/usage.html)一章。

