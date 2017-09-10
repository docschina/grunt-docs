# grunt.task

注册、执行和加载外部任务。

参见 [task lib source](https://github.com/gruntjs/grunt/blob/master/lib/grunt/task.js) 和 [task util lib source](https://github.com/gruntjs/grunt/blob/master/lib/util/task.js) 获取更多信息。

## The task API
当一个任务正在执行时，Grunt 通过`this` 对象向此任务函数暴露了很多任务特定的属性和方法。参见 [[Inside tasks{{深入任务内幕}}]]指南，这里可以找到完整的属性和方法列表。

很多属性和方法都可以通过 `this` 对象访问到。

注意，任何标记为的 ☃ (unicode snowman) 方法也可以直接通过 `grunt` 对象直接访问到。参见 [API首页](grunt) 以获取更多信息。

## 创建任务

### grunt.task.registerTask ☃ 
注册 "别名任务" 或 任务函数。此方法支持以下两种类型：

**别名任务**

如果指定了一个任务列表，那么，新注册的任务将会是这一个或多个任务的别名（alias）。当此"别名任务"执行时，`taskList`中的所有任务都将按指定的顺序依次执行。`taskList` 参数必须是一个任务数组。

```js
grunt.task.registerTask(taskName, taskList)
```

When the optional `description` string is passed it will be displayed when `grunt --help` is run:

```js
grunt.task.registerTask(taskName, description, taskList)
```

下面这个案例展示的是定义一个"default" 任务，当执行 Grunt 且不通过参数指定任务时， "jshint"、 "qunit"、"concat" 和 "uglify" 任务将自动执行：

```js
task.registerTask('default', ['jshint', 'qunit', 'concat', 'uglify']);
```

还可以指定任务的参数。下面的这个案例中，别名"dist"执行了 "concat" 和 "uglify" 这两个任务，并且都指定了"dist" 参数：

```js
task.registerTask('dist', ['concat:dist', 'uglify:dist']);
```

**任务函数**

如果传入`description`和`taskFunction`，每当任务运行时，指定的函数（`taskFunction`）都会被执行。此外，当执行 `grunt --help`时，前面指定的描述（`description`）就会显示出来。特定任务的属性和方法在任务函数内部通过`this`对象的属性即可访问。如果任务函数返回`false`表示任务失败。

注意，`grunt.task.registerMultiTask`方法将稍候介绍，它可以被用于定义一种特殊类型的任务，即"复合任务"。

```js
grunt.task.registerTask(taskName, description, taskFunction)
```

下面这个案例中，当 Grunt 运行`grunt foo:testing:123`时，日志输出`foo, testing 123`。如果运行这个任务时不带参数，如`grunt foo`，日志输出`foo, no args`。

```js
grunt.task.registerTask('foo', 'A sample task that logs stuff.', function(arg1, arg2) {
  if (arguments.length === 0) {
    grunt.log.writeln(this.name + ", no args");
  } else {
    grunt.log.writeln(this.name + ", " + arg1 + " " + arg2);
  }
});
```

参见 [创建任务](creating-tasks) 文档,查看更多任务实现案例和别名任务。

_此方法还可以通过 [grunt.registerTask](grunt)调用。_

### grunt.task.registerMultiTask ☃
注册一个 "复合任务（multi task）"。 复合任务是指在不指定目标（target）时，将依次执行其所包含的所有已命名的子属性（sub-properties） (也就是 目标) 。除了默认的属性和方法外，还可以通过`this`对象访问复合任务独有的属性。

大多数的contrib任务，包括 [jshint task](https://github.com/gruntjs/grunt-contrib-jshint)、[concat task](https://github.com/gruntjs/grunt-contrib-concat) 和 [uglify task](https://github.com/gruntjs/grunt-contrib-uglify) 都是复合任务。

```js
grunt.task.registerMultiTask(taskName, description, taskFunction)
```

给定以下配置信息，当执行`grunt log:foo`时，下面的复合任务将输出日志`foo: 1,2,3`；当执行`grunt log:bar`时，将输出日志`bar: hello world`。如果只是执行`grunt log`，那么，将先输出日志`foo: 1,2,3`，然后是`bar: hello world`，最后是`baz: false`。

```js
grunt.initConfig({
  log: {
    foo: [1, 2, 3],
    bar: 'hello world',
    baz: false
  }
});

grunt.task.registerMultiTask('log', 'Log stuff.', function() {
  grunt.log.writeln(this.target + ': ' + this.data);
});
```

参见 [创建任务](creating-tasks) 文档以获取更多复合任务的案例。

_此方法还可以通过 [grunt.registerMultiTask](grunt) 调用。_

### grunt.task.requires

Fail the task if some other task failed or never ran.

```js
grunt.task.requires(taskName);
```

### grunt.task.exists
*Added in 0.4.5*

Check with the name, if a task exists in the registered tasks. Return a boolean.

```js
grunt.task.exists(name)
```

### grunt.task.renameTask ☃
重命名任务。如果你希望覆盖某个任务的默认行为，并且希望保留原来的名字，这个函数将会很有用。

_注意，如果一个任务已经被重命名了， [this.name](inside-tasks#this.name) 和 [this.nameArgs](nside-tasks#this.nameArgs) 属性都会发生相应的变化。_

```js
grunt.task.renameTask(oldname, newname)
```

_此方法还可以通过 [grunt.renameTask](grunt) 调用。_

## 加载外部定义的任务
对于多数项目来说，[Gruntfile](getting-started) 文件中可能会定义很多任务。对于大型项目或者需要在多个项目中共享任务的情况，可以从一个或多个外部目录加载任务，或者从npm安装的 Grunt 插件加载任务。 

### grunt.task.loadTasks ☃
从指定的目录（注意：相对于 [Gruntfile](getting-started) 所在目录）加载任务相关的文件。此方法可以从本地Grunt插件加载任务相关的文件，只需指定包含"tasks"子目录的插件目录即可。

```js
grunt.task.loadTasks(tasksPath)
```

_此方法还可以通过 [grunt.loadTasks](grunt) 调用。_

### grunt.task.loadNpmTasks ☃
从指定的 Grunt 插件中加载任务。此插件必须通过npm安装到本地，并且是参照 [Gruntfile](getting-started) 文件的相对路径。Grunt插件还可以通过 [grunt-init grunt插件模版](https://github.com/gruntjs/grunt-init)创建： `grunt init:gruntplugin`。

```js
grunt.task.loadNpmTasks(pluginName)
```

_此方法还可以通过 [grunt.loadNpmTasks](grunt) 调用。_


## 队列任务
Grunt自动将命令行中指定的任务加入队列并执行，但是，一些独特的任务可以向队列中加入额外需要执行的任务。

### grunt.task.run
将一个或多个任务放入队列中。 `taskList` 中的每个任务都会按照其在队列中的顺序，在当前任务执行完毕后立即执行。任务列表可以是一个任务数组或单个任务。

```js
grunt.task.run(taskList)
```

### grunt.task.clearQueue
完全清空任务队列。除非将额外的任务加入队列，否则将不会执行任何任务。

```js
grunt.task.clearQueue()
```

### grunt.task.normalizeMultiTaskFiles
将目标（target）的配置对象标准化为一个src-dest文件映射数组。此方法在内部由复合任务系统的[this.files / grunt.task.current.files](grunt.task#wiki-this-files)属性调用。

```js
grunt.task.normalizeMultiTaskFiles(data [, targetname])
```