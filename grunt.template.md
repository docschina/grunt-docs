#grunt.template

可以手工调用模板函数处理模版字符串。另外，`config.get` 方法（被很多任务所使用）会自动解析 `<% %>` 类型的模版字符串，此类型的模版字符串是在 `Gruntfile` 中指的配置数据中定义的。

### grunt.template.process
处理一个 [Lo-Dash 模版](http://lodash.com/docs/#template) 字符串。`template` 参数将被递归处理，知道没有任何模版位置。

默认的数据对象是整个配置对象，但是，如果设置了`options.data`，则使用该对象。默认的模板分隔符是`<% %>`，但是，如果`options.delimiters`被设置为自定义的形式（通过 [`grunt.template.addDelimiters`](/api/grunt.template#grunt.template.adddelimiters) 进行设置），那么后续就会使用此模板分隔符。

```js
grunt.template.process(template [, options])
```

在模板内部暴露了`grunt`对象，因此你可以这样做`<%= grunt.template.tody('yyyy') %>`。_注意, 如果数据对象已经有了`grunt`属性，那么在模板内部将无法访问`grunt` API。_

在下面这个案例中，`baz` 属性被递归处理，直到再也没有多余的 `<% %>` 模版需要处理。

```js
var obj = {
  foo: 'c',
  bar: 'b<%= foo %>d',
  baz: 'a<%= bar %>e'
};
grunt.template.process('<%= baz %>', {data: obj}) // 'abcde'
```

### grunt.template.setDelimiters
设置[Lo-Dash模板](http://lodash.com/docs/#template)的分隔符为预定义的形式，以防需要手动调用`grunt.util._.template`。`config`分隔符默认是`<% %>`。

_你可能永远不会使用这个方法，因为你所使用的 `grunt.template.process` 在内部使用的就是这个方法。_

```js
grunt.template.setDelimiters(name)
```

### grunt.template.addDelimiters
为[Lo-Dash模板](http://lodash.com/docs/#template)添加一个命名分隔符。你或许不需要使用这个方法，因为内置的分割符应该足以满足需求了，但是你仍可以随时添加`{% %}` 或者`[% %]`风格的分隔符。

`name` 参数应当是唯一的，因为这也是我们通过 `grunt.template.setDelimiters` 获取分隔符的方式，并且后续作为 `grunt.template.process` 的一个选项来使用。

```js
grunt.template.addDelimiters(name, opener, closer)
```

在此实例中，如果我们希望使用 `{% %}` 形式的分隔符，我们需要如下这样设置：

```js
grunt.template.addDelimiters('myDelimiters', '{%', '%}')
```

## 助手函数

### grunt.template.date
使用[dateformat library](https://github.com/felixge/node-dateformat)格式化一个日期。

```js
grunt.template.date(date, format)
```

在这个案例中，指定的日期被格式化为 month/day/year。

```js
grunt.template.date(847602000000, 'yyyy-mm-dd') // '1996-11-10'
```

### grunt.template.today
使用 [dateformat library](https://github.com/felixge/node-dateformat)格式化当前日期。

```js
grunt.template.today(format)
```

在这个案例中，当前日期被格式化为4位数字表示的年份。

```js
grunt.template.today('yyyy') // '2017'
```

_（有人提醒我每年更新这个日期，好让此文档看上去像当前的新版本）_
