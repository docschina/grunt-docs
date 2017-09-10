#grunt.option

Grunt的option API被用来在多个任务之间共享参数、访问命令行中设置的参数。

一个简单的案例就是为一个目标（target）指定一个用于区别开发期还是过渡期的标志。在命令行中：`grunt deploy --target=staging` 会让`grunt.option('target')`返回`"staging"`。

下面这个 `Gruntfile` 案例展示了如何使用 `target` 选项：

```js
grunt.initConfig({
  compass: {
    dev: {
      options: {
        /* ... */
        outputStyle: 'expanded'
      },
    },
    staging: {
      options: {
        /* ... */
        outputStyle: 'compressed'
      },
    },
  },
});
var target = grunt.option('target') || 'dev';
grunt.registerTask('deploy', ['compass:' + target]);
```

当你执行 `grunt deploy` 时，你的样式表将默认为`dev`目标并且输出易于阅读的CSS格式代码。如果你运行 `grunt deploy --target=staging` ，`staging`目标会被执行，输出压缩之后的CSS。

`grunt.option` 还可以在task中使用，如下：

```js
grunt.registerTask('upload', 'Upload code to specified target.', function(n) {
  var target = grunt.option('target');
  // do something useful with target here
});
grunt.registerTask('deploy', ['validate', 'upload']);
```

_注意，boolean参数可以仅指定key，而省略value。例如，在命令行执行 `grunt deploy --staging` 将会使 `grunt.option('staging')` 返回 `true`。_


### grunt.option ☃
获取或设置一个选项。

```js
grunt.option(key[, val])
```

boolean类型的选项可以通过在 `key` 前添加 `no-` 来取消。案例如下：

```js
grunt.option('staging', false);
var isDev = grunt.option('no-staging');
// isDev === true
```

### grunt.option.init
初始化 `grunt.option`。如果省略 `initObject` ，option将被初始化为一个空对象，否则将被设置为 `initObject`。

```js
grunt.option.init([initObject])
```

### grunt.option.flags
将所有参数作为命令行参数数组返回。

```js
grunt.option.flags()
```
