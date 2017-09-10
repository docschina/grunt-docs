# grunt.file

这里提供了很多用于读写文件、遍历文件系统和通过模式匹配查找文件的方法。其中很多方法都是Node.js中的文件操作函数的封装，但是提供了额外的错误处理、日志记录和字符编码转换。 

_注意：所有的文件路径都是参照 `Gruntfile` 文件的相对路径，除非通过 `grunt.file.setBase` 函数或在命令行中指定 `--base` 参数改变当前工作目录。_

## 字符编码

### grunt.file.defaultEncoding
设置此属性可以改变所有 `grunt.file` 方法的默认编码。默认是 `'utf8'`。如果必须改变这个值，建议你在Gruntfile文件中尽可能早改变。

```js
grunt.file.defaultEncoding = 'utf8';
```

### grunt.file.preserveBOM
*添加于 0.4.2 版本*

是否在 `file.read` 时保留字节顺序标记（BOM）。

```js
grunt.file.preserveBOM = false;
```

## 读写文件

### grunt.file.read
读取并返回文件的内容。返回值为一个字符串，如果 `options.encoding` 为 `null` ，则返回一个 [Buffer](https://nodejs.org/docs/latest/api/buffer.html)。

```js
grunt.file.read(filepath [, options])
```

`options` 对象可以设置以下属性： 

```js
var options = {
  // If an encoding is not specified, default to grunt.file.defaultEncoding.
  // If specified as null, returns a non-decoded Buffer instead of a string.
  encoding: encodingName
};
```

### grunt.file.readJSON
读取一个文件的内容，将其按照JSON格式解析，返回结果。参见 `grunt.file.read` 获取其所支持的参数列表。

```js
grunt.file.readJSON(filepath [, options])
```

### grunt.file.readYAML
读取一个文件的内容，将其按照YAML格式解析，返回结果。参见 `grunt.file.read` 获取其所支持的参数列表。

```js
grunt.file.readYAML(filepath [, options])
```

### grunt.file.write
将指定的内容写入文件中，如果需要，将创建文件路径中所有不存在的目录。字符串将按照指定的字符编码进行编码，[Buffers](https://nodejs.org/docs/latest/api/buffer.html) 将会按照指定的方式写入磁盘。

_如果指定了 `--no-write` 命令行参数，将不会真正写入文件。_

```js
grunt.file.write(filepath, contents [, options])
```

`options` 对象可设置以下属性：

```js
var options = {
  // If an encoding is not specified, default to grunt.file.defaultEncoding.
  // If `contents` is a Buffer, encoding is ignored.
  encoding: encodingName
};
```

### grunt.file.copy
将原文件拷贝到指定路径，如果需要，将创建文件路径中所有不存在的目录

_如果指定了 `--no-write` 命令行参数，将不会真正写入文件。_

```js
grunt.file.copy(srcpath, destpath [, options])
```

`options` 对象可设置以下属性：

```js
var options = {
  // If an encoding is not specified, default to grunt.file.defaultEncoding.
  // If null, the `process` function will receive a Buffer instead of String.
  encoding: encodingName,
  // The source file contents, source file path, and destination file path
  // are passed into this function, whose return value will be used as the
  // destination file's contents. If this function returns `false`, the file
  // copy will be aborted.
  process: processFunction,
  // These optional globbing patterns will be matched against the filepath
  // (not the filename) using grunt.file.isMatch. If any specified globbing
  // pattern matches, the file won't be processed via the `process` function.
  // If `true` is specified, processing will be prevented.
  noProcess: globbingPatterns
};
```

### grunt.file.delete
删除指定的文件。文件和目录会被依次递归删除。

_Will not delete the current working directory or files outside the current working directory unless the `--force` command-line option is specified._

_如果指定了 `--no-write` 命令行参数，那么，文件路径将不会真的被删除。_

```js
grunt.file.delete(filepath [, options])
```

`options` 对象只可以设置以下属性：

```js
var options = {
  // Enable deleting outside the current working directory. This option may
  // be overridden by the --force command-line option.
  force: true
};
```

## 目录操作

### grunt.file.mkdir
工作方式类似 `mkdir -p`。创建一个目录和所有的中间目录。如果没有指定 `mode` ，默认是 `0777 & (~process.umask())`.

_如果没有 `--no-write` 命令行参数，目录不会被真正创建。_

```js
grunt.file.mkdir(dirpath [, mode])
```

### grunt.file.recurse
递归遍历整个目录，对每个文件都执行 `callback` 函数。

```js
grunt.file.recurse(rootdir, callback)
```

callback 函数接收以下参数：
```js
function callback(abspath, rootdir, subdir, filename) {
  // The full path to the current file, which is nothing more than
  // the rootdir + subdir + filename arguments, joined.
  abspath
  // The root director, as originally specified.
  rootdir
  // The current file's directory, relative to rootdir.
  subdir
  // The filename of the current file, without any directory parts.
  filename
}
```

## 模式匹配
有时单独指定所有原始文件路径是不现实的，因此，Grunt通过内置的[node-glob](https://github.com/isaacs/node-glob) 库支持文件名 expansion (或者叫做 globbing)  。

参见 [配置任务](configuring-tasks) 指南中的 "Globbing patterns" 章节以获取 globbing pattern 实例。


### grunt.file.expand
返回包含匹配给定通配符模式的文件或者目录路径的特殊数组。这个方法接收一个逗号分割的匹配模式或者一个匹配模式数组。如果路径匹配模式以`!`开头，它会从返回的数组排除所匹配的项。模式是按指定的顺序进行处理的， 因此包含和排除文件的顺序是很重要的。

```js
grunt.file.expand([options, ] patterns)
```

文件路径都是参照 `Gruntfile` 文件的相对路径，除非通过 `grunt.file.setBase` 或 `--base` 命令行参数修改了当前工作目录。

`options` 对象支持所有 [minimatch library](https://github.com/isaacs/minimatch) 的参数，也支持额外的一些，如下：

* `filter` E接受一个有效的 [fs.Stats 方法名](https://nodejs.org/docs/latest/api/fs.html#fs_class_fs_stats) 或者一个已经通过了`src`文件路径匹配的函数，这个函数会返回`true`或`false`。
* `nonull` 会保留`src`匹配模式，即使文件匹配失败。结合Grunt的`-verbose`标志，这个选项有助于文件路径问题的调试。
* `matchBase` 不带斜线的模式只会匹配基本的名称部分。例如，这会使`*.js`就像`**/*.js`一样。
* `cwd` 会让模式相对于当前路径进行模式匹配，所有返回的文件路径也是相对于当前路径的。

### grunt.file.expandMapping
返回一个`src-dest`文件映射对象的数组。通过所指定的模式来匹配每一个源文件，然后将匹配的文件路径加入指定的`dest`中(dest存放匹配的文件路径)。这个文件路径会按照指定的选项加工或者重命名过。 查看`grunt.file.expand`方法文档可以了解如何指定`patterns`和`options`

```js
grunt.file.expandMapping(patterns, dest [, options])
```

_注意：这个方法可以用于以编程的方式针对多任务的情况生成一个`files`数组，它会优先使用在[配置任务](/configuring-tasks)指南中"动态构建文件对象"一节所描述的语法。_

除了支持那些`grunt.file.expand`方法之外，`options`对象还支持下面这些属性:

```js
var options = {
  // The directory from which patterns are matched. Any string specified as
  // cwd is effectively stripped from the beginning of all matched paths.
  cwd: String,
  // Remove the path component from all matched src files. The src file path
  // is still joined to the specified dest.
  flatten: Boolean,
  // Remove anything after (and including) either the first or last "." in the
  // destination path (indicated by options.extDot), then append this value.
  ext: String,
  // *Added in 0.4.3*
  // Indicates where the period demarcating the extension is located. Can take:
  // - 'first' (extension begins after the first period in the file name)
  // - 'last' (extension begins after the last period)
  // Default: 'first'
  extDot: String,
  // If specified, this function will be responsible for returning the final
  // dest filepath. By default, it joins dest and matchedSrcPath like so:
  rename: function(dest, matchedSrcPath, options) {
    return path.join(dest, matchedSrcPath);
  }
};
```

### grunt.file.match
针对一个或者多个文件路径来匹配一个或者多个匹配模式。返回一个特殊的数组，这个数组包含与指定的通配符模式任意匹配的所有文件路径。`patterns`和`filepaths`参数可以是一个单一的字符串或者也可以是一个字符串数组.如果匹配模式以`!`开头，就会从返回的数组从排除模式匹配的路径。模式会按指定的顺序进行处理，因此包含和排除文件的顺序是重要的。

```js
grunt.file.match([options, ] patterns, filepaths)
```

`options`对象也支持[minimatch库](https://github.com/isaacs/minimatch)提供的所有选项。例如：如果`options.matchBase`为true，即使模式中不带斜线，这个模式也会匹配包含斜线的基本名称。例如：`*.js`模式将匹配`path/to/file.js`文件路径。

### grunt.file.isMatch
这个方法与`grunt.file.match`方法包含同样的签名和逻辑，但是如果它匹配任意文件，就会简单的返回`ture`，否则返回`false`。

## 判断文件类型

### grunt.file.exists
检测给定的路径是否存在，返回boolean类型的值。

和Node.js 中的 [path.join](https://nodejs.org/docs/latest/api/path.html#path_path_join_path1_path2) 方法一样，此方法将所有参数连接在一起，并对结果做规范化。

```js
grunt.file.exists(path1 [, path2 [, ...]])
```

### grunt.file.isLink
给定的路径是否是符号链接，返回boolean类型的值。

和 Node.js 中的 [path.join](https://nodejs.org/docs/latest/api/path.html#path_path_join_path1_path2) 方法一样，此方法此方法将所有参数连接在一起，并对结果做规范化。

```js
grunt.file.isLink(path1 [, path2 [, ...]])
```

如果路径不存在则返回false。

### grunt.file.isDir
指定的路径是否是一个目录？返回boolean类型的值。

和 Node.js 中的 [path.join](https://nodejs.org/docs/latest/api/path.html#path_path_join_path1_path2) 方法一样，此方法此方法将所有参数连接在一起，并对结果做规范化。

```js
grunt.file.isDir(path1 [, path2 [, ...]])
```

如果路径不存在它也会返回false。

### grunt.file.isFile
指定的路径是否是一个文件？ 返回boolean类型的值。

和 Node.js 中的 [path.join](https://nodejs.org/docs/latest/api/path.html#path_path_join_path1_path2) 方法一样，此方法此方法将所有参数连接在一起，并对结果做规范化。

```js
grunt.file.isFile(path1 [, path2 [, ...]])
```

如果路径不存在将返回false。

## 路径

### grunt.file.isPathAbsolute
指定的文件路径是否是绝对路径？ 返回boolean类型的值。

和 Node.js 中的 [path.join](https://nodejs.org/docs/latest/api/path.html#path_path_join_path1_path2) 方法一样，此方法此方法将所有参数连接在一起，并对结果做规范化。

```js
grunt.file.isPathAbsolute(path1 [, path2 [, ...]])
```

### grunt.file.arePathsEquivalent
所有给出的路径是否都是同一个路径？返回boolean类型的值。

```js
grunt.file.arePathsEquivalent(path1 [, path2 [, ...]])
```

### grunt.file.doesPathContain
所有descendant路径是否全部包含在指定的ancestor路径中？返回boolean类型的值。

_注意：不需要检查路径是否真的存在。_

```js
grunt.file.doesPathContain(ancestorPath, descendantPath1 [, descendantPath2 [, ...]])
```

### grunt.file.isPathCwd
指定的文件路径是否是CWD？返回boolean类型的值。

和 Node.js 中的 [path.join](https://nodejs.org/docs/latest/api/path.html#path_path_join_path1_path2) 方法一样，此方法此方法将所有参数连接在一起，并对结果做规范化。

```js
grunt.file.isPathCwd(path1 [, path2 [, ...]])
```

### grunt.file.isPathInCwd
指定的文件路径是否在在CWD中？注意：CWD不在CWD_中_ 。返回boolean类型的值。

和 Node.js 中的 [path.join](https://nodejs.org/docs/latest/api/path.html#path_path_join_path1_path2) 方法一样，此方法此方法将所有参数连接在一起，并对结果做规范化。

```js
grunt.file.isPathInCwd(path1 [, path2 [, ...]])
```

### grunt.file.setBase
改变Grunt的当前工作目录(CWD)。默认情况下，所有文件路径都是参照 `Gruntfile` 文件的相对路径。此函数和
 `--base` 命令行参数的工作方式一致。

```js
grunt.file.setBase(path1 [, path2 [, ...]])
```

和 Node.js 中的 [path.join](https://nodejs.org/docs/latest/api/path.html#path_path_join_path1_path2) 方法一样，此方法此方法将所有参数连接在一起，并对结果做规范化。

## 外部工具库
*不建议使用*

__下面列出的所有外部工具库已经不再建议使用了。__

请使用 __npm__ 管理项目中对第三方工具库的依赖。

例如，如果你需要使用 [Lo-Dash](https://www.npmjs.org/package/lodash)，首先通过 `npm install lodash` 安装，然后在 `Gruntfile` 文件中使用即可： `var _ = require('lodash');`

### grunt.file.glob
*不建议使用*

[glob](https://github.com/isaacs/node-glob) - File globbing utility.

### grunt.file.minimatch
*不建议使用*

[minimatch](https://github.com/isaacs/minimatch) - File pattern matching utility.

### grunt.file.findup
*不建议使用*

[findup-sync](https://github.com/cowboy/node-findup-sync) - Search upwards for matching file patterns.
