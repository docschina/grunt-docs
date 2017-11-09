
1. 通过 `npm install -g grunt-init` 命令安装 [grunt-init](https://github.com/gruntjs/grunt-init) 。
2. 通过 `git clone git://github.com/gruntjs/grunt-init-gruntplugin.git ~/.grunt-init/gruntplugin` 命令安装grunt插件模版。
3. 在一个空的目录中执行 `grunt-init gruntplugin` 。
4. 执行 `npm install` 命令以准备开发环境。
5. 为你的插件书写代码。
6. 执行 `npm publish` 命令将你创建的 Grunt 插件提发布npm！

## 注意

### 命名你的task

"grunt-contrib" 命名空间保留给 Grunt 团队维护的task使用，请给你自己的task起一个合适名字，并且避免使用被保留的命名空间。

### 调试
Grunt默认隐藏了error stack traces，但是可以通过 `--stack` 参数启用，方便你调试自己的task。如果你希望 Grunt 在出现错误时总是能记录下stack trace，可以在你的shell中创建一个命令别名（alias）。例如，在bash中，可以通过 `alias grunt='grunt --stack'` 命令创建一个别名。

### 存储任务文件

只在项目根目录中的 .grunt/[npm-module-name] 目录中存储数据文件，并在适当的时候将其清除。对于临时文件这并不是一个好的解决方案, 建议使用后面列出的几个常用npm模块（例如 [temporary](https://www.npmjs.org/package/temporary)、[tmp](https://www.npmjs.org/package/tmp)）来调用操作系统级别的临时目录功能。

### 避免改变当前工作目录：`process.cwd()`
默认情况下，包含gruntfile文件的目录被设置为当前工作目录。用户可以在自己的gruntfile中通过执行`grunt.file.setBase()` 改变改变当前工作目录，但是插件不应该改变它。

`path.resolve('foo')` 可以被用来获取'foo' 相对于 `Gruntfile` 所在目录的绝对路径。

### Creating your task

You might also want to know how to [create your own tasks](https://gruntjs.com/creating-tasks) or take a look at the [API](https://gruntjs.com/api) reference.
