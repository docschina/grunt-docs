# 配置任务

这个指南解释了如何使用 `Gruntfile` 来为你的项目配置task。如果你还不知道 `Gruntfile` 是什么，请先阅读 [[Getting Started{{快速入门}}]] 指南并看看这个[[Sample Gruntfile{{ Gruntfile 实例}}]]。

## Grunt配置
Grunt的task配置都是在 `Gruntfile` 中的`grunt.initConfig`方法中指定的。此配置主要是以任务名称命名的属性，也可以包含其他任意数据。一旦这些代表任意数据的属性与任务所需要的属性相冲突，就将被忽略。

此外，由于这本身就是JavaScript，因此你不仅限于使用JSON；你可以在这里使用任何有效的JavaScript。必要的情况下，你甚至可以以编程的方式生成配置。

```js
grunt.initConfig({
  concat: {
    // 这里是concat任务的配置信息。
  },
  uglify: {
    // 这里是uglify任务的配置信息
  },
  // 任意数据。
  my_property: 'whatever',
  my_src_files: ['foo/*.js', 'bar/*.js'],
});
```

## 任务配置和目标

当运行一个任务时，Grunt会自动查找配置对象中的同名属性。多任务（multi-task）可以通过任意命名的“目标（target）”来定义多个配置。在下面的案例中，`concat`任务有名为`foo`和`bar`两个目标，而`uglify`任务仅仅只有一个名为`bar`目标。

```js
grunt.initConfig({
  concat: {
    foo: {
      // concat task "foo" target options and files go here.
    },
    bar: {
      // concat task "bar" target options and files go here.
    },
  },
  uglify: {
    bar: {
      // uglify task "bar" target options and files go here.
    },
  },
});
```

同时指定任务（task）和目标（target），例如`grunt concat:foo`或者`grunt concat:bar`，将只会处理指定目标（target）的配置，而运行`grunt concat`将遍历_所有_目标（target）并依次处理。注意，如果一个任务使用[grunt.task.renameTask](grunt.task#grunt.task.renameTask)重命名过，Grunt将在配置对象中查找以_新的_任务名命名的属性。

## options属性
在一个任务配置中，`options`属性可以用来指定覆盖内置属性的默认值。此外，每一个目标（target）中还可以拥有一个专门针对此目标（target）的`options`属性。目标（target）级的平options将会覆盖任务级的options。

`options`对象是可选的，如果不需要，可以忽略。

```js
grunt.initConfig({
  concat: {
    options: {
      // 这里是任务级的Options，覆盖默认值 
    },
    foo: {
      options: {
        // "foo" target options may go here, overriding task-level options.
      },
    },
    bar: {
      // No options specified; this target will use task-level options.
    },
  },
});
```

## 文件
由于大多的任务都是执行文件操作，Grunt 有一个强大的抽象层用于声明任务应该操作哪些文件。这里有好几种定义**src-dest**(源文件-目标文件)文件映射的方式，均提供了不同程度的描述和控制操作方式。任何一种多任务（multi-task）都能理解下面的格式，所以你只需要选择满足你需求的格式就行。

All files formats support `src` and `dest` but the [Compact](configuring-tasks#compact-format) and [Files Array](configuring-tasks#files-array-format) formats support a few additional properties:

* `filter` 它通过接受任意一个有效的[fs.Stats方法名](https://nodejs.org/docs/latest/api/fs.html#fs_class_fs_stats)或者一个函数来匹配`src`文件路径并根据匹配结果返回`true`或者`false`。 [See examples](configuring-tasks#custom-filter-function)
* `nonull` 如果被设置为 `true`，未匹配的模式也将执行。结合Grunt的`--verbore`标志, 这个选项可以帮助用来调试文件路径的问题。
* `dot` 它允许模式模式匹配句点开头的文件名，即使模式并不明确文件名开头部分是否有句点。
* `matchBase`如果设置这个属性，缺少斜线的模式(意味着模式中不能使用斜线进行文件路径的匹配)将不会匹配包含在斜线中的文件名。 例如，`a?b` 将匹配 `/xyz/123/acb` 但不匹配 `/xyz/acb/123`。
* `expand` 处理动态的`src-dest`文件映射，更多的信息请查看[动态构建文件对象](configuring-tasks#building-the-files-object-dynamically)。
* 其他的属性将作为匹配项传递给底层的库。 请查看[node-glob][] 和[minimatch][] 文档以获取更多信息。

### Difference Between Grunt and Task Options
Most tasks perform file operations, so Grunt provides a built-in infrastructure to retrieve the files a task should process. The advantage is that this logic doesn't have to be implemented again by tasks authors. To allow a user to specify these files, Grunt provides options such as `nonull` and `filter`.

In addition to the files to work on, each task has its own specific needs. A task author may want to allow its user to configure some options to override the default behavior. These task-specific options shall not be confused with the Grunt options described before.

To further clarify this difference, let's see an example that uses [grunt-contrib-jshint](https://github.com/gruntjs/grunt-contrib-jshint):

```js
grunt.initConfig({
  jshint: {
    ignore_warning: {
      options: {
        '-W015': true,
      },
      src: 'js/**',
      filter: 'isFile'
    }
  }
});
```

This configuration employs the Grunt options `src` and `filter` to specify the files to process. It also uses the grunt-contrib-jshint task-specific option `-W015` to ignore a specific warning (the one having code `W015`).

### 简洁格式
这种形式允许每个目标对应一个**src-dest**文件映射。通常情况下它用于只读任务，比如[grunt-contrib-jshint](https://github.com/gruntjs/grunt-contrib-jshint)，它就只需要一个单一的`src`属性，而不需要关联的`dest`选项. 这种格式还支给每个`src-dest`文件映射指定额外的属性。

```js
grunt.initConfig({
  jshint: {
    foo: {
      src: ['src/aa.js', 'src/aaa.js']
    },
  },
  concat: {
    bar: {
      src: ['src/bb.js', 'src/bbb.js'],
      dest: 'dest/b.js',
    },
  },
});
```

### 文件对象格式
这种形式支持每个目标对应多个`src-dest`形式的文件映射，属性名就是目标文件，源文件就是它的值(源文件列表则使用数组格式声明)。可以使用这种方式指定数个`src-dest`文件映射， 但是不能够给每个映射指定附加的属性。

```js
grunt.initConfig({
  concat: {
    foo: {
      files: {
        'dest/a.js': ['src/aa.js', 'src/aaa.js'],
        'dest/a1.js': ['src/aa1.js', 'src/aaa1.js'],
      },
    },
    bar: {
      files: {
        'dest/b.js': ['src/bb.js', 'src/bbb.js'],
        'dest/b1.js': ['src/bb1.js', 'src/bbb1.js'],
      },
    },
  },
});
```

### 文件数组格式
这种形式支持每个目标对应多个`src-dest`文件映射，同时也允许每个映射拥有额外属性：

```js
grunt.initConfig({
  concat: {
    foo: {
      files: [
        {src: ['src/aa.js', 'src/aaa.js'], dest: 'dest/a.js'},
        {src: ['src/aa1.js', 'src/aaa1.js'], dest: 'dest/a1.js'},
      ],
    },
    bar: {
      files: [
        {src: ['src/bb.js', 'src/bbb.js'], dest: 'dest/b/', nonull: true},
        {src: ['src/bb1.js', 'src/bbb1.js'], dest: 'dest/b1/', filter: 'isFile'},
      ],
    },
  },
});
```

### 较老的格式
**dest-as-target**文件格式在多任务和目标出现之前是一个过渡形式，目标文件路径实际上就是目标名称。遗憾的是, 由于目标名称是文件路径，那么运行`grunt task:target`可能不合适。此外，你也不能指定一个目标级的`options`或者给每个`src-dest`文件映射指定额外属性。

此种格式已经不赞成使用，请尽量不要使用。

```js
grunt.initConfig({
  concat: {
    'dest/a.js': ['src/aa.js', 'src/aaa.js'],
    'dest/b.js': ['src/bb.js', 'src/bbb.js'],
  },
});
```

### 自定义过滤函数
`filter`属性可以给你的目标文件提供一个更高级的详细帮助信息。只需要使用一个有效的[fs.Stats 方法名](https://nodejs.org/docs/latest/api/fs.html#fs_class_fs_stats)。下面的配置仅仅清理一个与模式匹配的真实的文件：

```js
grunt.initConfig({
  clean: {
    foo: {
      src: ['tmp/**/*'],
      filter: 'isFile',
    },
  },
});
```

或者创建你自己的`filter`函数，根据文件是否匹配来返回`true`或者`false`。下面的例子将仅仅清理一个空目录：

```js
grunt.initConfig({
  clean: {
    foo: {
      src: ['tmp/**/*'],
      filter: function(filepath) {
        return (grunt.file.isDir(filepath) && require('fs').readdirSync(filepath).length === 0);
      },
    },
  },
});
```

Another example—which utilizes the [globbing](configuring-tasks#globbing-patterns) and [expand: true](configuring-tasks#building-the-files-object-dynamically) features—allows you to avoid overwriting files which already exist in the destination:

```js
grunt.initConfig({
  copy: {
    templates: {
      files: [{
        expand: true,
        cwd: ['templates/css/'],     // Parent folder of original CSS templates
        src: '**/*.css',             // Collects all `*.css` files within the parent folder (and its subfolders)
        dest: 'src/css/',            // Stores the collected `*.css` files in your `src/css/` folder
        filter: function (dest) {    // `dest`, in this instance, is the filepath of each matched `src`
          var cwd = this.cwd,        // Configures variables (these are documented for your convenience only)
              src = dest.replace(new RegExp('^' + cwd), '');
              dest = grunt.task.current.data.files[0].dest;
          return (!grunt.file.exists(dest + src));    // Copies `src` files ONLY if their destinations are unoccupied
        }
      }]
    }
  }
});
```

Keep in mind the above technique does not account for the [rename property](configuring-tasks#building-the-files-object-dynamically) when checking if the destination exists.

### 通配符模式
通常分别指定所有源文件路径是不切实际的，因此Grunt通过内置支持[node-glob][] 和 [minimatch][] 库来匹配文件名(又叫作`globbing`)。

[node-glob]: https://github.com/isaacs/node-glob
[minimatch]: https://github.com/isaacs/minimatch

然这并不是一个综合的匹配模式方面的教程，你只需要知道如何在文件路径匹配过程中使用它们即可：

* `*` 匹配任意数量的字符，但不匹配 `/`
* `?` 匹配单个字符，但不匹配 `/`
* `**` 匹配任意数量的字符，包括 `/`，只要它是路径中唯一的一部分
* `{}` 允许使用一个逗号分割的“或”表达式列表
* `!` 在模式的开头用于排除一个匹配模式所匹配的任何文件

每个人都需要知道的是：`foo/*.js`将匹配位于`foo/`目录下的所有的`.js`结尾的文件；而`foo/**/*js`将匹配`foo/`目录以_及其子目录_中所有以`.js`结尾的文件。

此外, 为了简化原本复杂的通配符模式，Grunt允许指定一个数组形式的文件路径或者一个通配符模式。所有模式按顺序处理，模式处理的过程中，带有`!`前缀的模式所匹配的文件将不包含在结果集中。 而且其结果集中的每一项也是唯一的。

例如：

```js
// 指定单个文件：
{src: 'foo/this.js', dest: ...}
// 指定一个文件数组：
{src: ['foo/this.js', 'foo/that.js', 'foo/the-other.js'], dest: ...}
// 使用一个匹配模式：
{src: 'foo/th*.js', dest: ...}

// 一个独立的node-glob模式：
{src: 'foo/{a,b}*.js', dest: ...}
// 也可以这样编写：
{src: ['foo/a*.js', 'foo/b*.js'], dest: ...}

// foo目录中所有的.js文件，按字母顺序排序：
{src: ['foo/*.js'], dest: ...}
// 首先是bar.js，接着是剩下的.js文件，并按字母顺序排序：
{src: ['foo/bar.js', 'foo/*.js'], dest: ...}

// 除bar.js之外的所有的.js文件，按字母顺序排序：
{src: ['foo/*.js', '!foo/bar.js'], dest: ...}
// 按字母顺序排序的所有.js文件，但是bar.js在最后。
{src: ['foo/*.js', '!foo/bar.js', 'foo/bar.js'], dest: ...}

// 模板也可以用于文件路径或者匹配模式中：
{src: ['src/<%= basename %>.js'], dest: 'build/<%= basename %>.min.js'}
// 它们也可以引用在配置中定义的其他文件列表：
{src: ['foo/*.js', '<%= jshint.all.src %>'], dest: ...}
```

更多关于通配符模式的语法，请查看[node-glob][] 和 [minimatch][] 的文档。

### 动态构建文件对象
当你希望处理大量的单个文件时，这里有一些附加的属性可以用来动态的构建一个文件列表。这些属性都可以用于  [Compact](configuring-tasks#compact-format) 和 [Files Array](configuring-tasks#files-array-format) 文件映射格式。

`expand` 设置为`true`将启用下面的选项：

* `cwd` 所有`src`指定的匹配都将相对于此处指定的路径（但不包括此路径） 。
* `src` 相对于`cwd`路径的匹配模式。
* `dest` 目标文件路径前缀。
* `ext` 对于生成的`dest`路径中所有实际存在文件，均使用这个属性值替换扩展名。
* `extDot` 用于指定标记扩展名的英文点号的所在位置。可以赋值 `'first'` （扩展名从文件名中的第一个英文点号开始） 或 `'last'` （扩展名从最后一个英文点号开始），默认值为 `'first'` *[添加于 0.4.3 版本]*
* `flatten` 从生成的`dest`路径中移除所有的路径部分。
* `rename` Embeds a customized function, which returns a string containing the new destination and filename. This function is called for each matched `src` file (after extension renaming and flattening). [More information](configuring-tasks#the-rename-property)

在下面的例子中，`uglify` 任务中的`static_mappings`和`dynamic_mappings`两个目标具有相同的`src-dest`文件映射列表, 这是因为任务运行时Grunt会自动展开`dynamic_mappings`文件对象为4个单独的静态`src-dest`文件映射--假设这4个文件能够找到。

可以指定任意静态`src-dest`和动态的`src-dest`文件映射相互结合。

```js
grunt.initConfig({
  uglify: {
    static_mappings: {
      // Because these src-dest file mappings are manually specified, every
      // time a new file is added or removed, the Gruntfile has to be updated.
      files: [
        {src: 'lib/a.js', dest: 'build/a.min.js'},
        {src: 'lib/b.js', dest: 'build/b.min.js'},
        {src: 'lib/subdir/c.js', dest: 'build/subdir/c.min.js'},
        {src: 'lib/subdir/d.js', dest: 'build/subdir/d.min.js'},
      ],
    },
    dynamic_mappings: {
      // Grunt will search for "**/*.js" under "lib/" when the "uglify" task
      // runs and build the appropriate src-dest file mappings then, so you
      // don't need to update the Gruntfile when files are added or removed.
      files: [
        {
          expand: true,     // Enable dynamic expansion.
          cwd: 'lib/',      // Src matches are relative to this path.
          src: ['**/*.js'], // Actual pattern(s) to match.
          dest: 'build/',   // Destination path prefix.
          ext: '.min.js',   // Dest filepaths will have this extension.
          extDot: 'first'   // Extensions in filenames begin after the first dot
        },
      ],
    },
  },
});
```

#### The rename Property
The `rename` property is unique, as the only valid value for it is a JavaScript function. Although the function returns a string, you cannot simply use a string as a value for `rename` (doing so results in an error: `Property 'rename' of object # is not a function`). In the following example, the `copy` task will create a backup of README.md.

```js
grunt.initConfig({
  copy: {
    backup: {
      files: [{
        expand: true,
        src: ['docs/README.md'],    // The README.md file has been specified for backup
        rename: function () {       // The value for rename must be a function
          return 'docs/BACKUP.txt'; // The function must return a string with the complete destination
        }
      }]
    }
  }
});
```

When the function is called, the `dest` and matched `src` path are passed in and can be used for returning the output string. In the following example, files are copied from the `dev` folder to the `dist` folder, and renamed to have the word "beta" removed .

```js
grunt.initConfig({
  copy: {
    production: {
      files: [{
        expand: true,
        cwd: 'dev/',
        src: ['*'],
        dest: 'dist/',
        rename: function (dest, src) {          // The `dest` and `src` values can be passed into the function
          return dest + src.replace('beta',''); // The `src` is being renamed; the `dest` remains the same
        }
      }]
    }
  }
});
```

If multiple matched `src` paths are renamed to an identical destination (i.e. if two different files get renamed to the same file), each output will be added to an array of sources for it.

## 模板
使用`<% %>`分隔符指定的模板会在任务从它们的配置中读取相应的数据时将自动扩展扫描。模板会被递归的展开，直到配置中不再存在遗留的模板相关的信息(与模板匹配的)。

整个配置对象决定了属性上下文(模板中的属性)。此外，在模板中使用`grunt`以及它的方法都是有效的，例如： `<%= grunt.template.today('yyyy-mm-dd') %>`。

* `<%= prop.subprop %>` 将会自动展开配置信息中的`prop.subprop`的值，不管是什么类型。像这样的模板不仅可以用来引用字符串值，还可以引用数组或者其他对象类型的值。
* `<% %>` 执行任意内联的JavaScript代码。对于控制流或者循环来说是非常有用的。

下面以`concat`任务配置为例，运行`grunt concat:sample`时将通过banner中的`/* abcde */`连同`foo/*.js`+`bar/*.js`+`bar/*.js`匹配的所有文件来生成一个名为`build/abcde.js`的文件。

```js
grunt.initConfig({
  concat: {
    sample: {
      options: {
        banner: '/* <%= baz %> */\n',   // '/* abcde */\n'
      },
      src: ['<%= qux %>', 'baz/*.js'],  // [['foo/*.js', 'bar/*.js'], 'baz/*.js']
      dest: 'build/<%= baz %>.js',      // 'build/abcde.js'
    },
  },
  //用于任务配置模板的任意属性
  foo: 'c',
  bar: 'b<%= foo %>d', // 'bcd'
  baz: 'a<%= bar %>e', // 'abcde'
  qux: ['foo/*.js', 'bar/*.js'],
});
```

## 导入外部数据
在下面的Gruntfile中，项目的元数据是从`package.json`文件中导入到Grunt配置中的，并且[grunt-contrib-uglify 插件](https://github.com/gruntjs/grunt-contrib-uglify)中的 `uglify` 任务被配置用于压缩一个源文件以及使用该元数据动态的生成一个banner注释。

Grunt有`grunt.file.readJSON`和`grunt.file.readYAML`两个方法分别用于引入JSON和YAML数据。

```js
grunt.initConfig({
  pkg: grunt.file.readJSON('package.json'),
  uglify: {
    options: {
      banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
    },
    dist: {
      src: 'src/<%= pkg.name %>.js',
      dest: 'dist/<%= pkg.name %>.min.js'
    }
  }
});
```