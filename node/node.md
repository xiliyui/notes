# Node.js
## Node.js 回调函数
回调函数在完成任务后就会被调用，Node 使用了大量的回调函数，Node 所有 API 都支持回调函数。
```
function foo1(name, age, callback) { }
function foo2(value, callback1, callback2) { }
```


## Node.js 事件循环
Node.js 使用事件驱动模型，当web server接收到请求，就把它关闭然后进行处理，然后去服务下一个web请求。    
当这个请求完成，它被放回处理队列，当到达队列开头，这个结果被返回给用户。    
这个模型非常高效可扩展性非常强，因为 webserver 一直接受请求而不等待任何读写操作。（这也称之为非阻塞式IO或者事件驱动IO）    
在事件驱动模型中，会生成一个主循环来监听事件，当检测到事件时触发回调函数。


## Node.js EventEmitter
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

### error 事件
EventEmitter 定义了一个特殊的事件 error，它包含了错误的语义，我们在遇到 异常的时候通常会触发 error 事件。   
当 error 被触发时，EventEmitter 规定如果没有响 应的监听器，Node.js 会把它当作异常，退出程序并输出错误信息。   
一般要为会触发 error 事件的对象设置监听器，避免遇到错误后整个程序崩溃。
```
const events = require('events')
const emitter = new events.EventEmitter()
emitter.emit('error')
```


## Node.js Buffer(缓冲区)
JavaScript 语言自身只有字符串数据类型，没有二进制数据类型。   
但在处理像TCP流或文件流时，必须使用到二进制数据。因此在 Node.js中，定义了一个 Buffer 类，该类用来创建一个专门存放二进制数据的缓存区。   
在 Node.js 中，Buffer 类是随 Node 内核一起发布的核心库。Buffer 库为 Node.js 带来了一种存储原始数据的方法，可以让 Node.js 处理二进制数据，每当需要在 Node.js 中处理I/O操作中移动的数据时，就有可能使用 Buffer 库。原始数据存储在 Buffer 类的实例中。一个 Buffer 类似于一个整数数组，但它对应于 V8 堆内存之外的一块原始内存。

### Buffer 与字符编码
Buffer实例一般用于表示编码字符的序列。通过使用显式的字符编码，就可以在Buffer实例与普通的JavaScript字符串之间进行相互转换。
```
const buf = Buffer.from('runoob', 'ascii')
// 输出 72756e6f6f62
console.log(buf.toString('hex'))
// 输出 cnVub29i
console.log(buf.toString('base64'))
```
#### Node.js 目前支持的字符编码包括：
- ascii  
仅支持7位ASCII数据。如果设置去掉高位的话，这种编码是非常快的。
- utf8  
多字节编码的 Unicode 字符。许多网页和其他文档格式都使用UTF-8。
- utf16le  
2或4个字节，小字节序编码的 Unicode 字符。支持代理对（U+10000 至 U+10FFFF）。
- ucs2  
utf16le 的别名。
- base64  
Base64编码。
- latin1    
一种把BUffer编码成一字节编码的字符串的方式。
- binary  
latin1的别名。
- hex    
将每个字节编码为两个十六进制字符。

### 创建 Buffer 类
- `Buffer.alloc(size[, fill[, encoding]])`  
返回一个指定大小的 Buffer 实例，如果没有设置 fill，则默认填满 0
- `Buffer.allocUnsafe(size)`   
返回一个指定大小的 Buffer 实例，但是它不会被初始化，所以它可能包含敏感的数据
- `Buffer.allocUnsafeSlow(size)`
- `Buffer.from(array)`   
返回一个被 array 的值初始化的新的 Buffer 实例（传入的 array 的元素只能是数字，不然就会自动被 0 覆盖）
- `Buffer.from(arrayBuffer[, byteOffset[, length]])`   
返回一个新建的与给定的 ArrayBuffer 共享同一内存的 Buffer。
- `Buffer.from(buffer)`   
复制传入的 Buffer 实例的数据，并返回一个新的 Buffer 实例
- `Buffer.from(string[, encoding])`   
返回一个被 string 的值初始化的新的 Buffer 实例

### 写入缓存区
`buf.write(string[, offset[, length]][, encoding])`
#### 参数
- string - 写入缓冲区的字符串
- offset - 缓冲区开始写入的索引值，默认为 0
- length - 写入的字节数，默认为 buffer.length
- encoding - 使用的编码。默认为 'utf8' 
#### 返回值
返回实际写入的大小，如果buffer空间不足，则只会写入部分字符串。 

### 从缓存区读取数据
`buf.toString([eoncoding[, start[, end]]])`
#### 参数
- encoding - 使用的编码。默认为 'utf8'
- start - 指定开始读取的索引位置，默认为 0
- end - 结束位置，默认为缓冲区的末尾
#### 返回值
解码缓存区数据并使用指定的编码返回字符串。

### 将 Buffer 转换为 JSON 对象
`bif.toJSON()`

### 缓冲区合并
`Buffer.concat(list[, toTotalLength])`
#### 参数
- list - 用于合并的 Buffer 对象数组列表
- totalLength - 指定合并后 Buffer 对象的总长度
#### 返回值
返回一个或多个成员合并的新 Buffer 对象。

### 缓冲区比较
`buf.compare(otherBuffer)`
#### 参数
- otherBuffer - 与 buf 对象比较的另一个 Buffer 对象。
#### 返回值
返回一个数字，表示 buf 在 otherBuffer 之前，之后或相同。

### 拷贝缓冲区
`buf.copy(targetBuffer[, targetStart[, sourceStart[, sourceEnd]]])`
#### 参数
- targetBuffer - 要拷贝的 Buffer 对象
- targetStart - 数字, 要拷贝的 Buffer 对象的指定位置, 可选, 默认: 0
- sourceStart - 数字, 被拷贝的 Buffer 对象的开始位置, 可选, 默认: 0
- sourceEnd - 数字, 可选, 被拷贝的 Buffer 对象的结束位置, 默认: buffer.length
#### 返回值
没有返回值。

### 缓冲区裁剪
`buf.slice([start[, end]])`
#### 参数
- start - 数字, 可选, 默认: 0
- end - 数字, 可选, 默认: buffer.length
#### 返回值
返回一个新的缓冲区，它和旧缓冲区值相同一块内存，但是从索引 start 到 end的位置裁剪。

### 缓冲区长度
`buf.length`