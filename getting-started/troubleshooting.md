# **解析**

## **通常解析问题**

* `--display-error-details` 参数会给我们提供更多细节
* 阅读[配置项](http://webpack.github.io/docs/configuration.html)从 `resolve` 部分开始了解解析相关的内容
  * 加载器有其自己的解析配置项 `resolveLoader`


## `npm link` **的模块无法找到其依赖**

模块解析的算法十分简单：模块依赖在每一个被依赖模块的父文件夹中的 `node_modules` 文件夹中查找。当你的 `npm link` 模块及其[**同伴依赖**](https://nodejs.org/en/blog/npm/peer-dependencies/)不在根目录下，就无法找到该模块（你也可以认为在 node.js 的设计中，使用了 `npm link` 的同伴依赖是不可用的）。应该注意的是，即使没有在 `package.json` 的模块中列出来，应用的依赖也是一种同伴依赖（尽管这不是一个完美的设计）。

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

### **文件的改动可被监听，但是没有文件被更新**

通过在运行 webpack 时加上 `-progress` 标记可以验证 webpack 确实没有感知到变动。如果在保存时看到了过程却无文件输出，那更有可能是修改了了一个配置文件，而非监听的文件。

```
webpack --watch --progress
```

### **没有足够的监听器**

验证你的系统上是否有足够的监听器，如果其值过低，webpack 的文件监听器将不会感知到变化：

```
cat /proc/sys/fs/inotify/max_user_watches
```

Arch 用户将 `fs.inotify.max_user_watches=524288` 添加到 `/etc/sysctl.d/99-sysctl.conf` 然后执行 `sysctl --system`。Ubuntu 用户和其他系统用户执行：`echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p`。

### **OS-X fsevents bug**

在 OS-X 的文件夹中可能会导致崩溃。可以参看这篇文章：

[OS X FSEvents 的 bug 可能阻止某些特定文件夹下的监控](http://feedback.livereload.com/knowledgebase/articles/86239-os-x-fsevents-bug-may-prevent-monitoring-of-certai)

### **Windows 路径**

webpack 在许多配置选项中都期望传入绝对路径。`__dirname + "/app/folder"` 的写法是错误的，因为 windows 使用 `\` 作为路径分隔符，这会使其无法正常工作。

使用正确的分隔符：例如 `path.resolve(__dirname, "app/folder")` 或者 `path.join(__dirname, "app", "folder")`。

### **Vim**

在一些机器上 vim 预先配置[备份拷贝（backupcopy）选项](http://vimdoc.sourceforge.net/htmldoc/options.html#'backupcopy')为**自动**。这可能会导致系统文件监听机制的问题。将选项切换到 `yes` 将确保生产文件拷贝，且原始文件在保存时被覆盖。

`:set backupcopy=yes`

### **在 WebStorm 中保存文件不会触发监听**

当使用 JetBrains 的 IDE WebStorm 时，你可能会发现保存文件时不会如期望的那样触发监听。可以尝试在设置中禁用 `safe write` 选项，这一选项决定了是否在覆盖源文件前先将文件先保存到一个临时位置。取消勾选 `File > Settings... > System Settings > Use "safe write" (save changes to a temporary file first)`（译者注：可以直接在 WebStorm 的配置中搜 `safe write`，取消勾选即可）。

