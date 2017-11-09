任务是Grunt的面包和奶油。就像你常用的工具，如： `jshint`  或 `nodeunit`。每当运行Grunt时, 你可以为其指定一个或多个任务, 这些任务用于告诉Grunt你想要它做什么事情。

如果你没有指定一个任务，并且你已经定义一个名为 "default" 的任务，那么该任务将会默认被执行（不用诧异，总要做点儿什么啊！）。

## 任务别名
如果指定了一个任务列表，新任务将是这一个或多个指定任务的别名。当运行此 "任务别名" 时，在 `taskList` 中指定的每个任务都会按照其出现的顺序依次执行。`taskList`参数必须时一个任务数组。

```js
grunt.registerTask(taskName, [description, ] taskList)
```

下面的任务别名案例中定义了一个 'default' 任务，如果运行Grunt时没有指定任何任务，它将自动执行'jshint'、'qunit'、'concat' 和 'uglify' 任务。

```js
grunt.registerTask('default', ['jshint', 'qunit', 'concat', 'uglify']);
```

还可以给任务指定参数。在下面的案例中，别名 "dist" 将执行 "concat" 和 "uglify" 两个任务，并且它们都带有一个 "dist"  参数：

```js
grunt.registerTask('dist', ['concat:dist', 'uglify:dist']);
```

## 多任务

当运行一个多任务时，Grunt会自动从项目的配置对象中查找同名属性。多任务可以有多个配置，并且可以使用任意命名的'targets'。

同时指定像`grunt concat:foo`或者`grunt concat:bar`这样的任务和目标，在运行时Grunt只会处理指定目标的配置；然而如果运行`grunt concat`，将会遍历所有的目标, 并按任务指定的顺序处理每个目标。注意，如果一个任务已经使用[grunt.task.renameTask](grunt.task#grunt.task.renameTask)重命名过，Grunt将会自动在配置对象中查找新任务名称属性。

大部分的contrib任务(主要是指官方提供的任务)，包括[grunt-contrib-jshint插件的jshint任务](https://github.com/gruntjs/grunt-contrib-jshint#jshint-task)，以及[grunt-contrib-concat插件的concat任务](https://github.com/gruntjs/grunt-contrib-concat#concat-task)都是多任务形式的。

```js
grunt.registerMultiTask(taskName, [description, ] taskFunction)
```

对于指定的配置，这里有一个案例演示了如果通过`grunt log:foo`运行Grunt，它会输出`foo: 1,2,3`；如果通过`grunt log:bar`来运行Grunt， 它会输出`bar: hello world`。然而如果通过`grunt log`运行Grunt, 它会输出`foo: 1,2,3`，然后是`bar: hello world`，最后是`baz: false`(任务目标会按照指定的顺序进行处理)。

```js
grunt.initConfig({
  log: {
    foo: [1, 2, 3],
    bar: 'hello world',
    baz: false
  }
});

grunt.registerMultiTask('log', 'Log stuff.', function() {
  grunt.log.writeln(this.target + ': ' + this.data);
});
```


## "基本" 任务
当一个基本任务执行时，Grunt并不会检查配置和环境 -- 它仅仅执行指定的任务函数，并传递任何使用冒号分割的参数作为函数的参数。

```js
grunt.registerTask(taskName, [description, ] taskFunction)
```

下面的案例中，如果执行 `grunt foo:testing:123`，将输出日志 `foo, testing 123`。  如果执行这个任务时不传递参数，只是执行 `grunt foo`，那么将输出日志  `foo, no args`。

```js
grunt.registerTask('foo', 'A sample task that logs stuff.', function(arg1, arg2) {
  if (arguments.length === 0) {
    grunt.log.writeln(this.name + ", no args");
  } else {
    grunt.log.writeln(this.name + ", " + arg1 + " " + arg2);
  }
});
```

## 自定义任务
你可以和任务一起疯狂。如果你的任务并没有遵循 "多任务" 结构，那就使用自定义任务。

```js
grunt.registerTask('default', 'My "default" task description.', function() {
  grunt.log.writeln('Currently running the "default" task.');
});
```

在一个任务内部，你可以执行其他的任务。

```js
grunt.registerTask('foo', 'My "foo" task.', function() {
  // Enqueue "bar" and "baz" tasks, to run after "foo" finishes, in-order.
  grunt.task.run('bar', 'baz');
  // Or:
  grunt.task.run(['bar', 'baz']);
});
```

任务也可以是异步的。

```js
grunt.registerTask('asyncfoo', 'My "asyncfoo" task.', function() {
  // Force task into async mode and grab a handle to the "done" function.
  var done = this.async();
  // Run some sync stuff.
  grunt.log.writeln('Processing task...');
  // And some async stuff.
  setTimeout(function() {
    grunt.log.writeln('All done!');
    done();
  }, 1000);
});
```

任务也可以访问它们自身名称和参数。

```js
grunt.registerTask('foo', 'My "foo" task.', function(a, b) {
  grunt.log.writeln(this.name, a, b);
});

// 用法：
// grunt foo
//   logs: "foo", undefined, undefined
// grunt foo:bar
//   logs: "foo", "bar", undefined
// grunt foo:bar:baz
//   logs: "foo", "bar", "baz"
```

如果记录到任何错误，那么任务就会失败。

```js
grunt.registerTask('foo', 'My "foo" task.', function() {
  if (failureOfSomeKind) {
    grunt.log.error('This is an error message.');
  }

  // 如果这个任务出现错误则返回false
  if (ifErrors) { return false; }

  grunt.log.writeln('This is the success message');
});
```

当任务失败时，所有后续任务都将终止，除非指定 `--force` 。

```js
grunt.registerTask('foo', 'My "foo" task.', function() {
  // Fail synchronously.
  return false;
});

grunt.registerTask('bar', 'My "bar" task.', function() {
  var done = this.async();
  setTimeout(function() {
    // Fail asynchronously.
    done(false);
  }, 1000);
});
```

任务还可以依赖于其他任务的成功执行。注意 `grunt.task.requires` 并不会真正的运行其他任务，它仅仅检查其它任务是否已经执行，并且没有失败。

```js
grunt.registerTask('foo', 'My "foo" task.', function() {
  return false;
});

grunt.registerTask('bar', 'My "bar" task.', function() {
  // 如果"foo"任务运行失败或者没有运行则任务失败。
  grunt.task.requires('foo');
  // 如果"foo"任务运行成功则执行这里的代码。
  grunt.log.writeln('Hello, world.');
});

// 用法：
// grunt foo bar
//   没有输出，因为"foo"失败。
//   ***Note: This is an example of space-separated sequential commands,
//   (similar to executing two lines of code: `grunt foo` then `grunt bar`)
// grunt bar
//   没有输出，因为"foo"从未运行。
```

如果任务需要的配置属性不存在，其也可能失败。

```js
grunt.registerTask('foo', 'My "foo" task.', function() {
  // Fail task if "meta.name" config prop is missing
  // Format 1: String
  grunt.config.requires('meta.name');
  // or Format 2: Array
  grunt.config.requires(['meta', 'name']);
  // Log... conditionally.
  grunt.log.writeln('This will only log if meta.name is defined in the config.');
});
```

任务还可以访问配置属性。

```js
grunt.registerTask('foo', 'My "foo" task.', function() {
  // 记录属性值，如果属性未定义（undefined）则返回null。
  grunt.log.writeln('The meta.name property is: ' + grunt.config('meta.name'));
  // 同样的记录属性值，如果属性未定义（undefined）则返回null。
  grunt.log.writeln('The meta.name property is: ' + grunt.config(['meta', 'name']));
});
```

在 [contrib tasks](https://github.com/gruntjs/) 中可以查看更多案例。
## CLI 参数 / 环境

通过 `process.env` 来访问[环境变量](https://en.wikipedia.org/wiki/Environment_variable)。

请参考 [使用命令行工具](https://gruntjs.com/using-the-cli)章节，查看完整的的命令行选项列表。

## 为什么我的异步task没有完成？
Chances are this is happening because you have forgotten to call the [this.async](https://gruntjs.com/api/inside-tasks#this.async) method to tell Grunt that your task is asynchronous. For simplicity's sake, Grunt uses a synchronous coding style, which can be switched to asynchronous by calling `this.async()` within the task body.

注意，传递 `false` 给 `done()` 函数就会告诉Grunt你的任务已经失败。

例如：

```js
grunt.registerTask('asyncme', 'My asynchronous task.', function() {
  var done = this.async();
  doSomethingAsync(done);
});
```

## 额外参考资料

如果你需要更多参考资料来创建自己的 task ，请参考 [API](https://gruntjs.com/api) 文档。
