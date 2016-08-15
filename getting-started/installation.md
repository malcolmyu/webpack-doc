## **node.js**

Install [node.js](http://nodejs.org/).

node.js comes with a package manager called `npm`.

## **webpack**

webpack can be installed through `npm`:

```
$ npm install webpack -g
```

webpack is now installed globally and the `webpack` command is available.

## **Use webpack in a project**

It’s the best to have webpack also as dependency in your project. Through this you can choose a local webpack version and will not be forced to use the single global one.

Add a `package.json` configuration file for `npm` with:

```
$ npm init
```

The answers to the questions are not so important if you don’t want to publish your project to npm.

Install and add `webpack` to the `package.json` with:

```
$ npm install webpack --save-dev
```

## **Versions**

There are two versions of webpack available. The stable one and a beta version. The beta version is marked with a `-beta` in the version string. The beta version may contain fragile changes or experimental features and is less tested. See [changelog](http://webpack.github.io/docs/changelog.html)for differences. For serious stuff you should use the stable version:

```
$ npm install webpack@1.2.x --save-dev
```

## **Dev Tools**

If you want to use dev tools you should install it:

```
$ npm install webpack-dev-server --save-dev
```

## **Continue reading**

You can continue reading [Usage](http://webpack.github.io/docs/usage.html).

