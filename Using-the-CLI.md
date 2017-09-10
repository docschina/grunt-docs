# 使用命令行工具

## 安装命令行工具

执行 `sudo npm install -g grunt-cli` (Windows users should omit "sudo ", and may need to run the command-line with elevated privileges)。

`grunt`命令行接口提供了一系列选项。你可以在你的终端中使用`grunt -h`查看这个选项。

### --help, -h
显示帮助信息。

### --base, -b
指定一个基本路径。默认情况下，所有文件路径都是相对于`Gruntfile`的。

还可以调用 `grunt.file.setBase(...)`

### --no-color
禁用彩色输出。

### --gruntfile
指定 `Gruntfile` 文件。

默认情况下，grunt会从当前目录或者父目录中寻找最近的`Gruntfile.js`或者`Gruntfile.coffee`文件。

### --debug, -d
对支持调试的任务启用调试模式。

### --stack
因警告或者致命错误退出时打印堆栈跟踪信息。

### --force, -f
一种强制跳过警告信息的方式。

如果像从警告中得到提示，就不要使用这个选项，可以根据提示信息修正代码。

### --tasks
指定一个包含可加载的任务和“额外”文件的目录。

还可以调用 `grunt.loadTasks(...)`

### --npm
在通过npm安装的插件中检查可加载的任何以及额外文件。

还可以调用 `grunt.loadNpmTasks(...)`

### --no-write
禁用写文件操作（可用于演示）。

### --verbose, -v

冗长模式（Verbose mode）。会输出很多的信息。

### --version, -V
打印grunt版本。结合 --verbose 一起使用可以获取更多信息。

### --completion
输出shell的自动补全规则。更多信息参考grunt-cli相关的文档。
