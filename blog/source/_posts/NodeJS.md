---
title: 'Node.JS大峡谷'
tags: [IT,JavaScript]
categories: 
- [编程]
mathjax: true
---

## NodeJS与JavaScript的区别

- 顶层对象
  - JavaScript：window
  - NodeJS：global

## 模块(modules)

>- 在node中，文件和模块是一一对应的，也就是一个文件就是一个模块
>
>- 每个模块都有自己的作用域
>
>- 我们通过`var`申明的变量并非全局，而是该模块作用域下的

### 模块调用(require)

```js
require('模块名称');
```

- 模块路径可以是一个以`/`开头，表示一个绝对路径

- 模块路径以`./`开头，表示当前目录下的相对路径

- 模块路径如果没有以`/`或者`./`开头，那么这个模块要么是*核心模块*要么是`node_ modules`文件夹下的

- 注意是`./`和没有`./`或`/`开头的路径，和我们常理上的使用结果是不一样的

> **模块加载机制：**
> 如果按照文件名顺序依次查找，若没有找到，则报错
> `文件名 > 文件名.js > 文件名.json > 文件名.node > Error`



### 模块数据(exports)

- module
  - 在Node中，每一个模块都有自己的作用域，同时还有一个module的变量，代表了对当前模块的引用，但module并不是全局对象，并且每个模块都有白己的独立的module对象
    - module.id
    - module.fileName
    - module.diyName
  - module.exports
  - exports===module.exports
- 外部模块通过`require()`方法加载模块，该函数返回的就是被加载模块的`module.exports`对象
- 我们可以通过`module.exports`或者`exports`对外提供模块内部变量的访问

> 注意的是最好不要直接覆盖`exports`或者`module.exports`，比如`exports=1` ,`module.exports=1`，这样做会破坏`exports`和`module .exports`的引用关系。



## global对象

- `_filename`属性

  - 返回当前执行的文件的文件路径，该路径是经过解析后的*绝对路径*，在模块中，该路径是模块文件的路径，此属性并非全局属性，而是模块的
  
- `_dirname`属性
  
  - 返回当前执行脚本文件所在目录的路径，该属性也是模块的，而非全局
  
- `setTimeout(cb, ms)`

- `clearTimeout(t)`

- `setInterval(cb, ms)`

- `clearlnterval(t)`

- `process`对象

  - `process`对象是一个全局对象，可以在任何地方都能
    访问到他，通过这个对象提供的属性和方法，使我
    们可以对当前运行的程序的进程进行访问和控制
    
  - 

    |        属性        |                  描述                  |
    | :----------------: | :------------------------------------: |
    |       `argv`       |    Array，一组包含命令行参数的数组     |
    |     `execPath`     |         开启当前进程的绝对路径         |
    |       `env`        |            返回用户环境信息            |
    |     `version`      |            返回node版本信息            |
    |     `versions`     |     返回node以及node依赖包版本信息     |
    |       `pid`        |            当前进程的`pid`             |
    |      `title`       |   当前进程的显示名称(Getter/Setter)    |
    |       `arch`       |  返回当前CPU处理器架构(arm/ia32/x64)   |
    |     `platform`     |          返回当前操作系统平台          |
    |      `cwd()`       |         返回当前进程的工作目录         |
    | `chdir(directory)` |         改变当前进程的工作目录         |
    |  `memoryUsage()`   | 返回node进程的内存使用情况，单位是byte |
    |    `exit(code)`    |                  退出                  |
    |     `kil(pid)`     |             向进程发送信息             |
    |      `stdin`       |               标准输入流               |
    |      `stdout`      |               标准输出流               |

  - Buffer类
    - 一个用于更好的操作二进制数据的类
    - 我们在操作文件或者网络数据的时候，其实操作的就是二进制数据流，Node为我们提供了一个更加方便的去操作这种数据流的类Buffer，是一个全局的类
```js
new Buffer(size);
new Buffer(array);
new Buffer(string,[encoding]);
```

|                             属性                             |                             描述                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                          buf.length                          |                      buffer的bytes大小                       |
|                          buf[index]                          |         获取或者设置在指定index索引位置的8位字节内容         |
|      buf.write(string, [offset]; [length], [encoding])       | 根据参数offset偏移量和指定的encoding编码方式，将参数string数据写入buffer |
|           buf.toString([encoding], [start], [end])           |    根据encoding参数(默认是'utf8')返回一个解码的string类型    |
|                         buf.toJSON()                         | 返回一个JSON表示的Buffer实例。JSON.stringify将会默认调用来字符串序列化这个Buffer实例 |
|                  buf.slice([start], [end])                   | 返回一个新的buffer,这个buffer将会和老的buffer引用相同的内存地址，注意:修改这个新的buffer实例slice切片，也会改变原来的buffer |
| buf.copy(targetBuffer, [targetStart], [sourceStart],[sourceEnd]) |                       进行buffer的拷贝                       |
|                 Buffer.isEncoding(encoding)                  |   如果给定的编码encoding是有效的，返回true，否则返回false    |
|                     Buffer.isBuffer(obj)                     |                 测试这个obj是否是一个Buffer                  |
|            Buffer.byteLength(string, [encoding])             |  将会返回这个字符串真实byte长度。encoding编码默认是: 'utf8'  |
|              Buffer.concat(list, [totalLength])              | 返回一个保存着将传入buffer数组中所有buffer对象拼接在一起的buffer对象 |




## 文件系统(File System)

该模块是核心模块，需要使用`require`导入后使用

```js
require('fs');
```

该模块提供了操作文件的一些API

|                            方法                            |                             描述                             |
| :--------------------------------------------------------: | :----------------------------------------------------------: |
|           fs.open(path, flags, [mode], callback)           |                     异步版的打开一个文件                     |
|              fs.openSync(path, flags, [mode])              |                      fs.open()的同步版                       |
|  fs.read(fd, buffer, offset, length, position, callback)   |               从指定的文档标识符fd读取文件数据               |
|     fs.readSync(fd, buffer, offset, length, position)      |          fs.read函数的同步版本，返回bytesRead的个数          |
| fs.write(fd, buffer, offset, length[, position], callback) |           通过文件标识fd，向指定的文件中写入buffer           |
|    fs.write(fd, data[, position[, encoding]], callback)    | 把data写 入到文档中通过指定的fd,如果data不是buffer对象的实例，则会把值强制转化成一个字符串。 |
|    fs.writeSync(fd, buffer, offset, length[, position])    |                     fs.write()的同步版本                     |
|       fs.writeSync(fd, data[, position[, encoding])        |                      fs.write()的同步版                      |
|                   fs.close(fd, callback)                   |                      关闭一个打开的文件                      |
|                      fs.closeSync(fd)                      |                     fs.close()的同步版本                     |
|      fs.writeFlie(ilename, data, [options], callback)      | 异步的将数据写入一个文件,如果文件不存在则新建,如果文件原先存在，会被替换。data可以是一个string，也可以是一个原生buffer。 |
|        fs.writeFileSync(filename, data, [options])         |     fs.writeFile的同步版本。注意：没有callback，也不需要     |
|     fs.appendFile(filename, data, [options], callback)     | 异步的将数据添加到一个文件的尾部，如果文件不存在，会创建一个新的文件。data可以是一个string，也可以是原生buffer。 |
|        fs.appendFileSync(filename, data, [options])        |                   fs.appendFile的同步版本                    |
|         fs.readFile(ilename, [options], callback)          |                  异步读取一个文件的全部内容                  |
|            fs.readFileSync(filename, [options])            |                    fs.readFile的同步版本                     |
|                  fs.exists(path, calback)                  |              检查指定路径的文件或者目录是否存在              |
|                    fs.existsSync(path)                     |                     fs.exists的同步版本                      |
|                 fs.unlink(path, callback)                  |                         删除一个文件                         |
|                    fs.unlinkSync(path)                     |                     fs.unlink的同步版本                      |
|           fs.rename(oldPath, newPath, callback)            |                            重命名                            |
|              fs.renameSync(oldPath, newPath)               |                    fs.rename()的同步版本                     |
|                  fs.stat(path, callback)                   |                         读取文件信息                         |
|                fs.statSync(path, callback)                 |                      fs.stat的同步版本                       |
|         fs.watch(filename, [options], [listener])          |     观察指定路径的改变，filename 路径可以是文件或者目录      |
|              fs.mkdir(path, [mode], callback)              |                          创建文件夹                          |
|                 fs.mkdirSync(path, [mode])                 |                      fs.mkdir的同步版本                      |
|                 fs.readdir(path, callback)                 |                          读取文件夹                          |
|                    fs.readdirSync(path)                    |                      fs.readdir同步版本                      |
|                  fs.rmdir(path, callback)                  |                          删除文件夹                          |
|                     fs.rmdirSync(path)                     |                     fs.rmdir的同步版本.                      |



## http模块

```js
//创建并返回一个HTTP服务器对象
//requestListener：监听到客户端连接的回调函数
var http = require('http');
var server = http.createServer([requestListener]);

//监听客户端连接请求，只有当调用了listen方法以后，服务器才开始工作
//port：监听的端口
//hostname：主机名(IP/域名)
//backlog：连接等待队列的最大长度
//callback：调用listen方法并成功开启监听以后，会触发一个listening事件，callback将 作为该事件的执行函数
server.listen(port, [hostname], [backlog], [callback]);
```

- listening事件：当server调用listen方法并成功开始监听以后触发的事件
- error事件：当服务开启失败的时候触发的事件
  - 参数err：具体的错误对象
- request事件：当有客户端发送请求到该主机和端口的请求的时候触发
  - 参数request对象：`http.IncomingMessage`的一个实例， 通过它我们可以获取到这次请求的一些信息，比如头信息，数据等.
    - httpVersion：使用的http协议的版本
    - headers：请求头信息中的数据
    - url：请求的地址
    - method：请求方式
  - 参数response对象：`http.ServerResponse`的一个实例，通过它我们可以向该次请求的客户端输出返回响应
    - write(chunk, [encoding])：发送一个数据块到响应正文中
    - end([chunk], [encoding])：当所有的正文和头信息发送完成以后调用该方法告诉服务器数据已经全部发送完成了，这个方法在每次完成信息发送以后必须调用，并且是最后调用
    - statusCode：该属性用来设置返回的状态码
    - setHeader(name, value)：设置返回头信息
    - writeHead(statusCode, [reasonPhrase], [headers])
      - 这个方法只能在当前请求中使用一次，并且必须在response.end()之前调用

## url模块

