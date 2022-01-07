# Node.js Stream(流)
Stream是一个抽象借接口，Node中有很多对象实现这个接口。例如，对http服务器发起请求的request对象就是一个Stream。

## Node.js,Stream有四种流类型：
- Readable - 可读操作
- Writable - 可写操作
- Duplex - 可读可写操作
- Transform - 操作被写入数据，然后读出结果

## Stream对象都是EventEmitter的实例，常用事件有：
- data - 当有数据可读时触发
- end - 没有更多的数据可读时触发
- error - 在接收和写入过程中发生错误时触发
- finish - 所有数据都被写入到底层系统时触发

## 从流中读取数据
```
cont readStream = fs.createReadStream('input.txt')
readStream.on('data', (chunk) => console.log(chunk))
```

## 写入流
```
const writeStream = fs.createWriteStream('output.txt')
writeStream.write('content')
```

## 管道流
提供了一个输出流到输入流的机制。
```
const readStream = fs.createReadStream('input.txt')
const writeStream = fs.createWriteStream('output.txt')
readStream.pipe(writeStream)
```

## 链式流
链式是通过连接输出流到另一个流并创建多个流操作链的机制。
- 压缩文件
```
const fs = require('fs')
const zlib = require('zlib')
fs.createReadStream('input.txt').pipe(zlib.createGzip()).pipe(fs.createWriteStream('input.zip'))
```
- 解压文件
```
const fs = require('fs')
const zlib = require('zlib')
fs.createReadStream('input.zip').pipe(zlib.createGunzip()).pipe(fs.createWriteStream('input.txt'))
```
