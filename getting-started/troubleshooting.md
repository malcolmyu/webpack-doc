# **解析**

## **通常解析问题**

* `--display-error-details` 参数会给我们提供更多细节
* 阅读[配置项](http://webpack.github.io/docs/configuration.html)从 `resolve` 部分开始了解解析相关的内容
  * 加载器有其自己的解析配置项 `resolveLoader`


## `npm link` **的模块无法找到其依赖**

模块解析的算法十分简单：模块依赖在 `node_modules` 文件夹和在每一个被依赖模块的父文件夹中查找。当你的 `npm link` 模块及其平级依赖不在根目录下，就无法找到该模块（你也可以认为在 node.js 的设计中，使用了 `npm link` 的 `peerDependencies` 是不可用的）。注意到应用的依赖，即使没有在 `package.json` 的模块中列出来，也是一种类型的平级依赖（尽管这不是一个完美的设计）。

但是我们可以很容易的在 webpack 中找到解决方案：将 `node_modules` 文件夹添加到项目中的解析路径中。有两个配置项可以配置这一点：`resolve.fallback` 和 `resolveLoader.fallback`。

这是一个配置的例子：

```
module.exports = {
  resolve: { fallback: path.join(__dirname, "node_modules") },
  resolveLoader: { fallback: path.join(__dirname, "node_modules") }
};
```

# **监听**

## **在监听改变的时候 webpack 不会重新编译**

### **File changes are being seen, just no files are being updated**

Verify that webpack is not being notified of changes by running with the –progress flag. If progress shows on save but no files are output, it is likely a configuration issue, not a file watching issue.

```
webpack --watch --progress
```

### **Not enough watchers**

Verify that if you have enough available watchers in your system. If this value is too low, the file watcher in Webpack won’t recognize the changes:

```
cat /proc/sys/fs/inotify/max_user_watches
```

Arch users, add `fs.inotify.max_user_watches=524288` to `/etc/sysctl.d/99-sysctl.conf` and then execute `sysctl --system`. Ubuntu users \(and possibly others\):`echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p`.

### **OS-X fsevents bug**

On OS-X folders can get corrupted. See this article:

[OS X FSEvents bug may prevent monitoring of certain folders](http://feedback.livereload.com/knowledgebase/articles/86239-os-x-fsevents-bug-may-prevent-monitoring-of-certai)

### **Windows paths**

webpack expects absolute paths for many config options. `__dirname + "/app/folder"` is wrong, because windows uses `\` as path separator. This breaks some stuff.

Use the correct separators. I.e. `path.resolve(__dirname, "app/folder")` or `path.join(__dirname, "app", "folder")`.

### **Vim**

On some machines Vim is preconfigured with the [backupcopy option](http://vimdoc.sourceforge.net/htmldoc/options.html#'backupcopy') set to **auto**. This could potentially cause problems with the system’s file watching mechanism. Switching this option to `yes` will make sure a copy of the file is made and the original one overwritten on save.

`:set backupcopy=yes`

### **File saves in WebStorm don’t trigger the watcher**

When using the JetBrains WebStorm IDE, you may find that saving changed files does not trigger the watcher as you might expect. Try disabling the `safe write` option in the settings, which determines whether files are saved to a temporary location first before the originals are overwritten: uncheck`File > Settings... > System Settings > Use "safe write" (save changes to a temporary file first)`.

