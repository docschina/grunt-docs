
各色工具函数/库，包括 Lo-Dash、Async 和 Hooker。

### grunt.util.kindOf
返回给定值的"类型（kind）"。就像`typeof`，但是其返回的是内部的`[Class](class/)`值。可能返回的结果是`"number"`、`"string"`、`"boolean"`、`"function"`、`"regexp"`、`"array"`、`"date"`、`"error"`、`"null"`、`"undefined"` 和可以表示一切类型的 `"object"`。

```js
grunt.util.kindOf(value)
```

### grunt.util.error
返回一个新的Error实例（也可以抛出）与相应的消息。如果指定的是Error对象而不是`message`，则返回对象。

另外，如果为 `origError` 参数指定的是Error对象，并且使用 `--stack` 选项运行Grunt，则输出原始的Error堆栈。

```js
grunt.util.error(message [, origError])
```

### grunt.util.linefeed
将换行符转换为当前系统所采用的形式（Window上是`\r\n`，其他系统为`\n`）。

### grunt.util.normalizelf
对于一个给定的字符串，将其所有换行符转换为当前系统所采用的形式，然后返回一个新的字符串。（Window上是`\r\n`，其他系统为`\n`）

```js
grunt.util.normalizelf(string)
```

### grunt.util.recurse
递归嵌套的对象和数组，为每个非对象值执行`callbackFunction`。如果`continueFunction`返回`false`, 给定的对象或值将会被跳过。

```js
grunt.util.recurse(object, callbackFunction, continueFunction)
```

### grunt.util.repeat
返回被重复`n`次的字符串`str`。

```js
grunt.util.repeat(n, str)
```

### grunt.util.pluralize
给定一个`"a/b"`形式的`str`，如果`n`为`1`，返回`"a"`，否则返回`"b"`。如果不能使用'/'，也可以指定一个自定义的分隔符。

```js
grunt.util.pluralize(n, str, separator)
```

### grunt.util.spawn
生成一个子进程，并跟踪其stdout、stderr和退出码。此方法返回子进程的引用。当子进程退出时，`doneFunction` 函数被调用。

```js
grunt.util.spawn(options, doneFunction)
```

`options` 对象可以指定以下属性：

```js
var options = {
  // The command to execute. It should be in the system path.
  cmd: commandToExecute,
  // If specified, the same grunt bin that is currently running will be
  // spawned as the child command, instead of the "cmd" option. Defaults
  // to false.
  grunt: boolean,
  // An array of arguments to pass to the command.
  args: arrayOfArguments,
  // Additional options for the Node.js child_process spawn method.
  opts: nodeSpawnOptions,
  // If this value is set and an error occurs, it will be used as the value
  // and null will be passed as the error value.
  fallback: fallbackValue
};
```

`doneFunction` 函数可以接收以下参数：

```js
function doneFunction(error, result, code) {
  // If the exit code was non-zero and a fallback wasn't specified, an Error
  // object, otherwise null.
  error
  // The result object is an object with the properties .stdout, .stderr, and
  // .code (exit code).
  result
  // When result is coerced to a string, the value is stdout if the exit code
  // was zero, the fallback if the exit code was non-zero and a fallback was
  // specified, or stderr if the exit code was non-zero and a fallback was
  // not specified.
  String(result)
  // The numeric exit code.
  code
}
```

### grunt.util.toArray
对于传入的数组或类数组对象，返回一个数组。对于将`arguments`对象转换为数组是非常有用的。

```js
grunt.util.toArray(arrayLikeObject)
```

### grunt.util.callbackify
标准化"返回值"和"传递结果给回调"的函数，总是传递一个结果给指定的回调函数。如果原始函数返回一个值，该值将即刻传递给回调函数,，并指定为最后一个参数，在所有的预定义参数之后。如果原始函数传递一个值给回调函数,，它也会继续照样如此。

```js
grunt.util.callbackify(syncOrAsyncFunction)
```

下面这个例子也许能够更好的说明：

```js
function add1(a, b) {
  return a + b;
}
function add2(a, b, callback) {
  callback(a + b);
}

var fn1 = grunt.util.callbackify(add1);
var fn2 = grunt.util.callbackify(add2);

fn1(1, 2, function(result) {
  console.log('1 plus 2 equals ' + result);
});
fn2(1, 2, function(result) {
  console.log('1 plus 2 equals ' + result);
});
```

## 内部工具库

### grunt.util.namespace
此内部工具库用于解析对象中深度嵌套的属性。

### grunt.util.task
用于task执行的内部工具库。

## 外部工具库
*不建议使用*

__下面列出的所有外部工具库已经不再建议使用了。__

请使用 __npm__ 管理项目中对第三方工具库的依赖。

例如，如果你需要使用 [Lo-Dash](https://www.npmjs.org/package/lodash)，首先通过 `npm install lodash` 安装，然后在 `Gruntfile` 文件中使用即可： `var _ = require('lodash');`

### grunt.util._
*不建议使用*

[Lo-Dash](http://lodash.com/) - 很多有用的数组、函数和对象工具方法。
[Underscore.string](https://github.com/epeli/underscore.string) - 很多实用的字符串工具函数。

`grunt.util._.str` is available for methods that conflict with existing Lo-Dash methods.

### grunt.util.async
*不建议使用*

[Async](https://github.com/caolan/async) - 对node和浏览器都适用的异步工具。

#### grunt.util.hooker
*不建议使用*

[JavaScript Hooker](https://github.com/cowboy/javascript-hooker) - 用于调试和做些其他事情的钩子（hook）函数。
