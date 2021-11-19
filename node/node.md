# Node.js
## Node.js回调函数
回调函数在完成任务后就会被调用，Node 使用了大量的回调函数，Node 所有 API 都支持回调函数。
```
function foo1(name, age, callback) { }
function foo2(value, callback1, callback2) { }
```


## Node.js时间循环
Node.js 使用事件驱动模型，当web server接收到请求，就把它关闭然后进行处理，然后去服务下一个web请求。    
当这个请求完成，它被放回处理队列，当到达队列开头，这个结果被返回给用户。    
这个模型非常高效可扩展性非常强，因为 webserver 一直接受请求而不等待任何读写操作。（这也称之为非阻塞式IO或者事件驱动IO）    
在事件驱动模型中，会生成一个主循环来监听事件，当检测到事件时触发回调函数。
### EventEmitter 类
events 模块只提供了一个对象：events.EventEmitter。EventEmitter 的核心就是事件触发与事件监听器功能的封装。