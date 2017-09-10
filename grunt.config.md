#grunt.config

从 `Gruntfile` 中获取针对当前项目的配置数据。

注意，任何标记为 ☃ (unicode snowman) 的方法也是可以直接通过 `grunt` 对象访问的；任何标记为 ☆ (white star) 的方法都可以在task内部通过 `this` 对象访问的。请知晓。

## 初始化配置数据
_注意，下面列出的方法也可以通过 `grunt` 对象访问，访问形式为 `grunt.initConfig`。_

### grunt.config.init ☃
为当前项目初始化一个配置对象。其中传入的 `configObject` 参数可以用在后续的task中，可以通过 `grunt.config` 方法访问。几乎每个项目的 `Gruntfile` 都会调用此方法。

```js
grunt.config.init(configObject)
```

注意，任何 `<% %>` 模板字符串只会在取到配置数据后才被处理。

下面的案例展示了针对 [grunt-contrib-jshint插件](https://github.com/gruntjs/grunt-contrib-jshint) 中的 `jshint` task的配置数据：

```js
grunt.config.init({
  jshint: {
    all: ['lib/*.js', 'test/*.js', 'Gruntfile.js']
  }
});
```

查看 [Getting started](getting-started) 指南可以获取更多的配置案例。

_此方法还可以以  `grunt.initConfig` 的形式访问。_


## 获取配置数据
The following methods allow Grunt configuration data to be accessed either via dot-delimited string like `'pkg.author.name'` or via array of property name parts like `['pkg', 'author', 'name']`.

Note that if a specified property name contains a `.` dot, it must be escaped with a literal backslash, eg. `'concat.dist/built\\.js'`. If an array of parts is specified, Grunt will handle the escaping internally with the `grunt.config.escape` method.

### grunt.config
从项目的 Grunt 配置中获取或者设置一个值。这个方法作为其他方法的别名；如果传递两个参数，`grunt.config.set`被调用，另一方面`grunt.config.get`也被调用。Get or set a value from the project's grunt configuration. This method serves as an alias to other methods; if two arguments are passed, `grunt.config.set` is called, otherwise `grunt.config.get` is called.

```js
grunt.config([prop [, value]])
```

### grunt.config.get
Get a value from the project's Grunt configuration. If `prop` is specified, that property's value is returned, or `null` if that property is not defined. If `prop` isn't specified, a copy of the entire config object is returned. Templates strings will be recursively processed using the `grunt.config.process` method.

```js
grunt.config.get([prop])
```

### grunt.config.process
Process a value, recursively expanding `<% %>` templates (via the `grunt.template.process` method) in the context of the Grunt config, as they are encountered. this method is called automatically by `grunt.config.get` but _not_ by `grunt.config.getRaw`.

```js
grunt.config.process(value)
```

If any retrieved value is entirely a single `'<%= foo %>'` or `'<%= foo.bar %>'` template string, and the specified `foo` or `foo.bar` property is a non-string (and not `null` or `undefined`) value, it will be expanded to the _actual_ value. That, combined with grunt's task system automatically flattening arrays, can be extremely useful.

### grunt.config.getRaw
Get a raw value from the project's Grunt configuration, without processing `<% %>` template strings. If `prop` is specified, that property's value is returned, or `null` if that property is not defined. If `prop` isn't specified, a copy of the entire config object is returned.

```js
grunt.config.getRaw([prop])
```

### grunt.config.set
给当前项目的 Grunt 配置中的某个属性设置一个值。

```js
grunt.config.set(prop, value)
```

注意，任何 `<% %>` 模板字符串只会在取到配置数据后才被处理。

### grunt.config.escape
忽略给定的`propString`中的`.`点号。这应该用于包含点号的属性名。Escape `.` dots in the given `propString`. This should be used for property names that contain dots.

```js
grunt.config.escape(propString)
```

### grunt.config.merge
*Added in 0.4.5*

Recursively merges properties of the specified `configObject` into the current project configuration. Array and plain object properties are merged recursively while other value types are overridden.

```js
grunt.config.merge(configObject)
```

You can use this method to append configuration options, targets, etc., to already defined tasks, for example:

```js
grunt.config.merge({
  watch: {
    files: ["path/to/files"],
    tasks: ["task"]
  }
});
```

Array values are merged based on their index. Consider the following code:

```js
grunt.initConfig({
  jshint: {
    files: ['Gruntfile.js', 'src/**/*.js'],
  }
);

var config = {
  jshint: {
    files: ['hello.js'],
  }
};

grunt.config.merge(config);
```

It'll result in the configuration shown below:

```js
jshint: {
  files: ['hello.js', 'src/**/*.js'],
}
```

In conclusion, the first value of the `files` array defined in the `config` variable (`hello.js`) overriddes the first value specified in the `initConfig` configuration call (`Gruntfile.js`).

## Requiring Config Data
_注意，下面列出的方法都可以在task内部通过 `this` 对象访问，访问形式为 `this.requiresConfig`。_

### grunt.config.requires ☆
如果需要的配置属性有一个或多个不存在、值为`null` 或 `undefined`，当前task将失败。此方法可以指定一个或多个字符串、配置属性数组作为参数。

```js
grunt.config.requires(prop [, prop [, ...]])
```

_此方法在task内部以 `this.requiresConfig` 形式调用。_