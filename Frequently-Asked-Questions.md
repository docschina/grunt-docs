#常见问题

## 如何安装grunt?
对于常规的安装说明，请阅读[[Getting Started{{{快速入门}}]]指南。如果在阅读完之后你需要更多的详细信息，你可以阅读更详细的的[[Installing grunt{{安装 Grunt}}]]指南。

###什么时候我将可以使用开发中的'某某'特性?
在[[Installing grunt{{安装 Grunt}}]]指南中介绍了如何安装已发布的和未发布的开发版本的Grunt。

###Grunt可以在Windows上工作吗?
Grunt可以很好的在windows上工作，因为[Node.js](https://nodejs.org/)和[npm](https://www.npmjs.com/)都能够很好的在windows上工作。通常情况下，问题在于[Cygwin](http://www.cygwin.com/)，因为它捆绑着一个较老版本的Node.js。

避免这个问题最好的办法是使用[msysGit installer](http://msysgit.github.com/)安装二进制的`git`和使用[Node.js installer](https://nodejs.org/#download)去安装二进制的`node`和`npm`，然后使用内置的[Windows command prompt](http://www.cs.princeton.edu/courses/archive/spr05/cos126/cmd-prompt.html) 或 [PowerShell](http://support.microsoft.com/kb/968929) 去替代Cygwin。

## 为什么我的异步任务不能执行完毕？
这是因为你忘记调用 [this.async](grunt.task#wiki-this-async) 方法来告知Grunt你的task是异步执行的。为了简化模型，Grunt采用同步模式的编码风格，你可以通过在任务中调用 `this.async()` 切换到异步模式。

注意，如果task执行失败，可以传递 `false` 给 `done()` 函数告知Grunt。

案例：

```js
grunt.registerTask('asyncme', 'My asynchronous task.', function() {
  var done = this.async();
  doSomethingAsync(done);
});
```

## 如何启用shell中的tab键自动补全功能?
为了给grunt增加tab键自动补功能，可以在你的`~/.bashrc`文件中添加下面一行代码：

```bash
eval "$(grunt --completion=bash)"
```

当然，假设你已经使用`npm install -g grunt`在全局安装好了Grunt。因为Grunt目前仅仅支持bash命令。

## 我如让多个任务共享参数？

虽然每个任务可以使用它自己的参数，但是，这里有几个方法允许你在多个task中共享参数。

### "动态的" 任务别名
**这是多个任务共享参数的首选方法**

鉴于[任务别名](grunt#wiki-grunt-registerTask)是很简单的，一个普通的task可以使用[grunt.task.run](grunt.task#wiki-grunt-task-run)让一个函数作为“动态的”任务别名。在下面这个案例中，在命令行中执行`grunt build:001`，最终效果是执行`foo:001`、`bar:001` 和 `baz:001`这三个task。

```js
grunt.registerTask('build', 'Run all my build tasks.', function(n) {
  if (n == null) {
    grunt.warn('Build num must be specified, like build:001.');
  }
  grunt.task.run('foo:' + n, 'bar:' + n, 'baz:' + n);
});
```

### -- 选项

多个任务共享参数的方式是使用[grunt.option](grunt#wiki-grunt-option)。在这里有一个例子，在命令行中执行`grunt deploy --target=staging`会让`grunt.option('target')`返回`"staging"`。

```js
grunt.registerTask('upload', 'Upload code to specified target.', function() {
  var target = grunt.option('target');
  // do something useful with target here
});
grunt.registerTask('deploy', ['validate', 'upload']);
```

_注意，布尔类型的参数可以使用一个没有值的键。例如，在命令行中执行`grunt deploy --staging`会让`grunt.option('staging')` 返回`true`。_

### 全局和配置

在其他情况下，你可能希望暴露一个设置配置或者全局的值方法。 在这种情况下，可以在注册任务时设置其参数作为一个全局对象的或者项目配置的值。

在下面的例子中，在命令行运行`grunt set_global:name:peter set_config:target:staging deploy`会导致`global.name`的值为`"peter"`以及`grunt.config('target')`将会返回`"staging"`。由此推断，`deploy`任务就可以使用这些值。

```js
grunt.registerTask('set_global', 'Set a global variable.', function(name, val) {
  global[name] = val;
});

grunt.registerTask('set_config', 'Set a config property.', function(name, val) {
  grunt.config.set(name, val);
});
```

## 当出现错误时如何获取调用栈的追踪信息？

使用 `--stack` 参数就可以看到调用栈的追踪信息了。例如：`grunt task --stack` 。

## 为什么出现 "Maximum call stack size exceeded（超出最大调用栈大小）" 的错误？

你可能是为某个任务创建的别名和其他任务重名了。
例如：`grunt.registerTask('uglify', ['uglify:my_target']);` 应该是 `grunt.registerTask('myUglify', ['uglify:my_target']);`。

## 如何卸载或移除不需要的插件？

至少有两种方法。一种方法时利用 `npm uninstall [GRUNT_PLUGIN] --save-dev` 指令，这将从 `package.json` 文件和 `node_modules` 目录下同时移除指定的插件。另一种方法时手工从 `package.json` 文件中删除依赖项，然后执行 `npm prune` 指令。

## 出现错误 "Fail to install with npm error: No compatible version found"

请确保安装了最新稳定版本的 [NPM 和 Node.JS](https://nodejs.org/)。


***


## grunt 0.3 的相关问题

###在Windows的 Grunt 0.3中，为什么当我尝试运行grunt时我的JS编辑器会打开?

如果你在[Gruntfile](getting-started)所在的目录中时，当你输入grunt时Windows会尝试去执行_那个文件_。因此你需要输入`grunt.cmd`。

另一个选择是使用`DOSKEY`命令去创建一个Grunt宏，请参考[这篇文章](https://gist.github.com/vladikoff/38307908088d58af206b)。这样就可以使用`grunt`替代`grunt.cmd`了。

可以使用所示如下的`DOSKEY`命令:

```
DOSKEY grunt=grunt.cmd $*
```
