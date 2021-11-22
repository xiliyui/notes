# Node.js
## Node.js回调函数
回调函数在完成任务后就会被调用，Node 使用了大量的回调函数，Node 所有 API 都支持回调函数。
```
function foo1(name, age, callback) { }
function foo2(value, callback1, callback2) { }
```


## Node.js事件循环
Node.js 使用事件驱动模型，当web server接收到请求，就把它关闭然后进行处理，然后去服务下一个web请求。    
当这个请求完成，它被放回处理队列，当到达队列开头，这个结果被返回给用户。    
这个模型非常高效可扩展性非常强，因为 webserver 一直接受请求而不等待任何读写操作。（这也称之为非阻塞式IO或者事件驱动IO）    
在事件驱动模型中，会生成一个主循环来监听事件，当检测到事件时触发回调函数。
### EventEmitter 类
events 模块只提供了一个对象：events.EventEmitter。EventEmitter 的核心就是事件触发与事件监听器功能的封装。   
创建 eventEmitter 对象   
`const eventEmitter = new events.EventEmitter()`

EventEmitter 的每个事件由一个事件名和若干个参数组成，事件名是一个字符串，通常表达一定的语义。对于每个事件，EventEmitter 支持若干个事件监听器。   
当事件触发时，注册到这个事件的事件监听器被依次调用，事件参数作为回调函数参数传递。    
```
const emitter = new events.EventEmitter()
emitter.on('someEvent', (arg1, arg2) => {
  console.log('listener1', arg1, arg2)
})
emitter.on('someEvent', (arg1, arg2) => {
  console.log('listener2', arg1, arg2)
})
emitter.emit('someEvent', 'params1', 'params2')
```
运行结果：
```
listener1 params1 params2
listener2 params1 params2
```

### 方法
- `addListener(event, listener)`   
为指定事件添加一个监听器到监听器数组的尾部。
- `on(event, listener)`   
为指定事件注册一个监听器，接受一个字符串event和一个回调函数。
- `once(event, listener)`   
为指定事件注册一个单次监听器，即监听器最多只会触发一次，触发后立刻解除该监听器。
- `removeListener(event, listener)`   
移除指定事件的某个监听器，监听器必须是该事件已经注册过的监听器。
- `removeAllListener(event, listener)`
移除所有事件的所有监听器，如果指定事件，则移除指定事件的所有监听器。
- `setMaxListener(n)`   
默认情况下，`EventEmitters`如果添加的监听器超过10个就会输出警告信息。`setMaxListeners`函数用于改变监听器的默认限制的数量。
- `listeners(event)`   
返回指定事件的监听器数组。
- `emit(event, [arg1], [arg2], [...])`   
按监听器的顺序执行每个监听器，如果事件有注册监听返回true，否则返回false

### 类方法
- `listenerCount(emitter, event)`   
返回指定事件的监听器数量
```
events.EventEmitter.listenerCount(emitter, eventName) // 已废弃，不推荐
events.emitter.listenerCount(eventName) // 推荐
```