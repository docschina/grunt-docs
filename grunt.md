
Grunt通过`grunt`对象暴露所有方法和属性，并将此对象赋予`module.exports`函数，这些方法和属性都将传递到你的[Gruntfile](getting-started)、Grunt插件或者[task文件](creating-tasks)中。

以下所有的方法几乎都是在别处定义的，但是为了方便使用，也在`grunt`对象中做了定义。详细的解释和案例请参阅各个api单独的文档。

## Config

### grunt.initConfig
_此方法是 [grunt.config.init](grunt.config#grunt.config.init) 方法的别名（alias）。_


## 创建task

### grunt.registerTask
_此方法是 [grunt.task.registerTask](grunt.task#grunt.task.registerTask) 方法的别名（alias）。_

### grunt.registerMultiTask
_此方法是 [grunt.task.registerMultiTask](grunt.task#grunt.task.registerMultiTask) 方法的别名（alias）。_

### grunt.renameTask
_此方法是 [grunt.task.renameTask](grunt.task#grunt.task.renameTask) 方法的别名（alias）。_

## Loading Externally-Defined Tasks

### grunt.loadTasks
_此方法是 [grunt.task.loadTasks](grunt.task#grunt.task.loadTasks) 方法的别名（alias）。_

### grunt.loadNpmTasks
_此方法是 [grunt.task.loadNpmTasks](grunt.task#grunt.task.loadNpmTasks) 方法的别名（alias）。_


## 警告和致命错误

### grunt.warn
_此方法是 [grunt.fail.warn](grunt.fail#grunt.fail.warn) 方法的别名（alias）。_

### grunt.fatal
_此方法是 [grunt.fail.fatal](grunt.fail#grunt.fail.fatal) 方法的别名（alias）。_


## 命令行参数

### grunt.option
检索命令行参数的值，例如`debug`。注意对于每个命令行参数，都可以做相反的测试，例如`no-debug`。

```js
grunt.option(optionName)
```

## 杂项

### grunt.package
`package.json` 中存储的元数据，其类型是对象。

```js
grunt.package
```

### grunt.version
当前 Grunt 的版本，类型是字符串。它仅仅是`grunt.package.version`属性的缩写。

```js
grunt.version
```
