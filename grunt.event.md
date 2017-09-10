# grunt.event

在这个页面中仅仅列出了最重要的方法，完整的 [EventEmitter2 API][ee2] 在 `grunt.event` 对象中都有定义。事件命名空间可以使用 `.`(英文句号)做分隔，并且可以使用命名空间通配符。

*注意：Grunt目前并不发出任何事件，但是在你自己的task中仍然是有用的。*

[ee2]: https://github.com/hij1nx/EventEmitter2

### grunt.event.on
为指定的事件添加一个监听器，并将此监听器放倒监听器数组的尾部。

```js
grunt.event.on(event, listener)
```

### grunt.event.once
为指定的事件添加一个执行 **一次** 的监听器。次监听器只在事件头一次被触发后才会被调用，并且执行完之后被移除。

```js
grunt.event.once(event, listener)
```

### grunt.event.many
为指定的事件添加一个监听器，并在被移除之前执行 **n 次** 。

```js
grunt.event.many(event, timesToListen, listener)
```

### grunt.event.off
从监听器数组中移除所有监听某个指定事件的监听器。

```js
grunt.event.off(event, listener)
```

### grunt.event.removeAllListeners
移除所有的监听器，或者某个指定事件的所有监听器。

```js
grunt.event.removeAllListeners([event])
```

### grunt.event.emit
依次执行每一个可能监听此事件名的监听器，并将参数列表传给每个事件监听器。

```js
grunt.event.emit(event, [arg1], [arg2], [...])
```
