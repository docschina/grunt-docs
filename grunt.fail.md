
用于发生致命的错误时。

参见 [fail lib source](https://github.com/gruntjs/grunt/blob/master/lib/grunt/fail.js) 以获取更多信息。

## The fail API

如果任务内部某些代码已经（或即将）崩溃，可能导致Grunt强行中止。参见 [退出码文档](Exit-Codes) 以获取完整的退出码（exit code）列表。

注意，任何带有 ☃ (unicode snowman) 标记的方法都可以直接通过 `grunt` 对象访问。参见 [API首页](grunt) 以获取更多信息。

### grunt.fail.warn ☃
显示一条警告信息然后立即退出Grunt。如果在命令行中指定了`--force` 选项，Grunt将继续执行后续任务。`error` 参数可以是字符串信息或error对象。

```js
grunt.fail.warn(error [, errorcode])
```

如果在命令行中指定 `--stack` 参数，并且给定error对象，stack trace 将会被记入日志。

_此方法还可以通过 `grunt.warn` 访问。_

### grunt.fail.fatal ☃
显示一条警告信息然后立即退出Grunt。`error` 参数可以是字符串信息或error对象。

```js
grunt.fail.fatal(error [, errorcode])
```

如果在命令行中指定 `--stack` 参数，并且给定error对象，stack trace 将会被记入日志。

出现致命（fatal）错误时会发出一声蜂鸣声，除非指定了 `--no-color` 参数。

_此方法还可以通过 `grunt.fatal` 访问。_
