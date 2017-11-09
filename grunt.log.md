
向控制台输出信息。

参见 [log lib source](https://github.com/gruntjs/grunt/blob/master/lib/grunt/log.js) 以获取更多信息。

## The log API
Grunt的输出应当看上去一致、美观。因此， 就有了这些log方法和一些有用的模式。所有用于输出日志的方法都可以链式调用。

_注意：只有在命令行中指定 `--verbose` 选项时，所有 `grunt.verbose` 中的方法才会输出日志，并且完全像 `grunt.log` 中的方法一样工作。_

### grunt.log.write / grunt.verbose.write
输出指定的 `msg` 字符串日志，结尾不带换行符（newline）。

```js
grunt.log.write(msg)
```

### grunt.log.writeln / grunt.verbose.writeln
输出指定的 `msg` 字符串日志，结尾带换行符（newline）。

```js
grunt.log.writeln([msg])
```

### grunt.log.error / grunt.verbose.error
如果省略`msg` 字符串，将会以红色字体输出`ERROR`，否则输出`>> msg`，并且尾部带有换行符。

```js
grunt.log.error([msg])
```

### grunt.log.errorlns / grunt.verbose.errorlns
使用`grunt.log.error`记录一个错误日志，使用`grunt.log.wraptext`可以将日志以每行80个字符的形式输出。

```js
grunt.log.errorlns(msg)
```

### grunt.log.ok / grunt.verbose.ok
如果省略`msg`字符串，将会以绿色字体输出`OK`, 否则输出`>> msg`，并且尾部带有换行符。

```js
grunt.log.ok([msg])
```

### grunt.log.oklns / grunt.verbose.oklns
使用`grunt.log.ok`记录一条ok消息，使用`grunt.log.wraptext`可以将日志以每行80个字符的形式输出。

```js
grunt.log.oklns(msg)
```

### grunt.log.subhead / grunt.verbose.subhead
记录指定的`msg`字符串并**加粗**，尾部带有换行符。

```js
grunt.log.subhead(msg)
```

### grunt.log.writeflags / grunt.verbose.writeflags
记录`obj`的属性列表（用于调试标志最好）。

```js
grunt.log.writeflags(obj, prefix)
```

### grunt.log.debug / grunt.verbose.debug
记录一条调试信息，但是，仅当在命令行中指定 `--debug`选项时才会输出。

```js
grunt.log.debug(msg)
```

## Verbose 和 Notverbose
所有`grunt.verbose`下可用的日志记录方法的工作都酷似它们所对应的`grunt.log`方法，但是它们只在指定`--verbose`命令行选项的情况下才一样。还有一个对应"notverbose"适用于`grunt.log.notverbose`和`grunt.log.verbose.or`。实际上，`.or`属性也可以用于在`verbose`和`notverbose`两者之间有效的进行切换。

### grunt.verbose / grunt.log.verbose
这个对象包含`grunt.log`下的所有方法，但是只在指定`--verbose`命令行选项情况下它才会输出日志信息。

```js
grunt.verbose
```

### grunt.verbose.or / grunt.log.notverbose
这个对象也包含`grunt.log`下的所有方法，但是只在不指定`--verbose`命令行选项情况下它才会输出日志信息。

```js
grunt.verbose.or
```

## 工具方法
这些方法实际上不记录日志，它们只返回字符串，返回的字符串可以用于其他方法。

### grunt.log.wordlist
Returns a comma-separated list of `arr` array items.
`arr` 数组中的条目将会以逗号分割的形式返回。

```js
grunt.log.wordlist(arr [, options])
```

`options` 对象拥有以下属性和默认值：

```js
var options = {
  // The separator string (can be colored).
  separator: ', ',
  // The array item color (specify false to not colorize).
  color: 'cyan',
};
```

### grunt.log.uncolor
从字符串中移除所有颜色信息，使其适合检测其 `.length` 或写入日志文件。、

```js
grunt.log.uncolor(str)
```

### grunt.log.wraptext
以 `width` 个字符为一组将 `text` 字符串进行分解并添加  `\n` 字符，除非绝对必要，否则将尽量确保不会从中间截断单词。

```js
grunt.log.wraptext(width, text)
```

### grunt.log.table
以 `width` 个字符为一组将 `text` 字符串进行分解。Wrap `texts` array of strings to columns `widths` characters wide. A wrapper for the `grunt.log.wraptext` method that can be used to generate output in columns.

```js
grunt.log.table(widths, texts)
```

## 案例

通常的模式是，只有在 `--verbose` 模式或发生错误时才输出日志，如下所示：

```js
grunt.registerTask('something', 'Do something interesting.', function(arg) {
  var msg = 'Doing something...';
  grunt.verbose.write(msg);
  try {
    doSomethingThatThrowsAnExceptionOnError(arg);
    // Success!
    grunt.verbose.ok();
  } catch(e) {
    // Something went wrong.
    grunt.verbose.or.write(msg).error().error(e.message);
    grunt.fail.warn('Something went wrong.');
  }
});
```

解释以上代码：

1. `grunt.verbose.write(msg);` 只有在 `--verbose` 模式时才会输出日志信息（没有换行符）。
2. `grunt.verbose.ok();` 以绿色输出日志信息，末尾输出换行符。
3. `grunt.verbose.or.write(msg).error().error(e.message);` 做了以下几件事：
  1. `grunt.verbose.or.write(msg)` 如果不在 `--verbose` 模式则输出日志信息，然后返回 `notverbose` 对象。
  2. `.error()` 以红色输出ERROR日志，结尾输出换行符，然后返回 `notverbose` 对象。
  3. `.error(e.message);` 输出实际的错误信息（并返回 `notverbose` 对象）。
4. `grunt.fail.warn('Something went wrong.');` 以嫩黄色输出警告信息。除非在命令行指定了 `--force` 选项，否则输出退出码1，然后退出 Grunt。

查看 [grunt-contrib-* 任务的源码](https://github.com/gruntjs) 以获取更多案例。
