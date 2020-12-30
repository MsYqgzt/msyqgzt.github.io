---
title: Node.JS大峡谷
tags: [编程,前端]
categories: 
- [JavaScript]
- [Node]
mathjax: true
date: 2020-09-24
---

## NodeJS与JavaScript的一些区别

- 顶层对象
  - JavaScript：window
  - NodeJS：global

## 模块(modules)

- 在node中，文件和模块是一一对应的，**一个文件即一个模块**，我们通常会把实现同一类功能的代码写在同一个文件里面。

- 每个模块都有自己的作用域，比如变量、`exports`属性等，因此访问其他模块的内容就得先调用模块。

  可以将数据结构大致整理为:

$$
\begin{split}
	项目\begin{cases}
		模块1\begin{cases}
			module.exports\\\\
			变量\\\\
			函数\\\\
			…
		\end{cases}\\\\
		模块2\begin{cases}
			module.exports\\\\
			变量\\\\
			函数\\\\
			…
		\end{cases}\\\\
		…
	\end{cases}
\end{split}
$$


- 我们通过`var`申明的变量并非全局，而是该模块作用域下的，更建议使用`let`

### 模块调用(require)

```js
require('模块路径/模块名称');
```

>**路径的写法：**
>
>- 模块路径可以是一个以`/`开头，表示一个绝对路径
>- 模块路径以`./`开头，表示当前目录下的相对路径
>- 模块路径如果没有以`/`或者`./`开头，那么这个模块要么是*核心模块*要么是`node_ modules`文件夹下的
>- 注意是`./`和没有`./`或`/`开头的路径，和我们常理上的使用结果是不一样的
>
> **模块加载机制：**
> 如果按照文件名顺序依次查找，若没有找到，则报错
> `filename > filename.js > filename.json > filename.node > Error`



### 模块数据(exports)

- module
  - 在Node中，每一个模块都有自己的作用域，同时**还有一个module的变量，代表了对当前模块的引用**，但module并不是全局对象，并且**每个模块都有白己的独立的module对象**
    - module.id
    - module.fileName
    - module.diyName
    - 这些并不是固定关键字，类似模块本身的属性定义，属性名是自定义的
  - `module.exports`
  - `exports === module.exports`
- 外部模块通过`require()`方法加载模块，**该函数返回的就是被加载模块的`module.exports`对象**
- 我们可以通过`module.exports`或者`exports`对外提供模块内部变量的访问

> 需要注意的是，最好不要直接覆盖`exports`或者`module.exports`，比如`exports=1` ,`module.exports=1`，这样做会破坏`exports`和`module .exports`的引用关系。

#### 举个🌰

通常我们会在项目里配置一些信息，这些都写在`config.js`中，配置后的信息可以赋予`exports`供其他模块调用

`config.js`：

```js
let config = {
    server: {
        'host': 'localhost',
        'port': 1080
    },

    scaffolds: {
        'type': 'folder',
        'name': 'css',
        'content': {
            'type': 'file',
            'name': 'main.css',
            'content': '/* main style */'
        },

        'type': 'file',
        'name': 'index.html',
        'content': '<h1>Hello World</h1>'
    }
    /* …… */
}

//将变量数据赋予当前模块数据
exports = config;
```

在其他模块中调用（这里假设模块文件都在同一路径目录下）

```js
let config = require("./config.js");
//调用模块数据
console.log(config.server.host);
console.log(config.server.port);
//……
```



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
    访问到他，通过这个对象提供的属性和方法，使我们可以对当前运行的程序的进程进行访问和控制
    
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

## arguments对象

> **非箭头函数：**
>
> ```js
> function func(a, b, c) {
>     /*……*/
> }
> ```
>
> **箭头函数：**
>
> ```js
> (a, b, c) => {
>     /*……*/
> }
> ```
>
> 

`arguments`对象是**所有（非箭头）函数**中都可用的**局部变量**。你可以使用`arguments`对象在函数中引用函数的参数。此对象包含传递给函数的每个参数，第一个参数在索引0处。

例如，如果一个函数传递了三个参数，你可以以如下方式引用他们：

```js
arguments[0]
arguments[1]
arguments[2]
```

参数也可以被设置：

```js
arguments[1] = 'new value';
```



```js
function func1(a, b, c) {
  console.log(arguments[0]);
  // expected output: 1

  console.log(arguments[1]);
  // expected output: 2

  console.log(arguments[2]);
  // expected output: 3
}

func1(1, 2, 3);
```



### 举几个🌰

#### 遍历参数求和

```js
function add() {
    var sum =0,
        len = arguments.length;
    for(var i=0; i<len; i++){
        sum += arguments[i];
    }
    return sum;
}
add()                           // 0
add(1)                          // 1
add(1,2,3,4);                   // 10
```

#### 定义连接字符串的函数

这个例子定义了一个函数来连接字符串。

这个函数唯一正式声明了的参数是一个字符串，该参数指定一个字符作为衔接点来连接字符串。

该函数定义如下：

```js
function myConcat(separator) {
  var args = Array.prototype.slice.call(arguments, 1);
  return args.join(separator);
}
```

你可以传递任意数量的参数到该函数，并使用每个参数作为列表中的项创建列表。

```js
// returns "red, orange, blue"
myConcat(", ", "red", "orange", "blue");

// returns "elephant; giraffe; lion; cheetah"
myConcat("; ", "elephant", "giraffe", "lion", "cheetah");

// returns "sage. basil. oregano. pepper. parsley"
myConcat(". ", "sage", "basil", "oregano", "pepper", "parsley");
```



#### 定义创建HTML列表的方法

这个例子定义了一个函数通过一个字符串来创建HTML列表。这个函数唯一正式声明了的参数是一个字符。当该参数为 "`u`" 时，创建一个无序列表 (项目列表)；当该参数为 "`o`" 时，则创建一个有序列表 (编号列表)。

该函数定义如下：

```js
function list(type) {
  var result = "<" + type + "l><li>";
  var args = Array.prototype.slice.call(arguments, 1);
  result += args.join("</li><li>");
  result += "</li></" + type + "l>"; // end list

  return result;
}
```

你可以传递任意数量的参数到该函数，并将每个参数作为一个项添加到指定类型的列表中。

例如：

```js
var listHTML = list("u", "One", "Two", "Three");

/* listHTML is:
"<ul><li>One</li><li>Two</li><li>Three</li></ul>"
*/
```



## 文件系统(File System)

该模块是核心模块，需要使用`require`导入后使用

```js
require('fs');
```

该模块提供了操作文件的一些API

|                            方法                            |                             描述                             |
| :--------------------------------------------------------: | :----------------------------------------------------------: |
|           fs.open(path, flags, [mode], callback)           |                      异步地打开一个文件                      |
|              fs.openSync(path, flags, [mode])              |                      fs.open()的同步版                       |
|  fs.read(fd, buffer, offset, length, position, callback)   |               从指定的文档标识符fd读取文件数据               |
|     fs.readSync(fd, buffer, offset, length, position)      |          fs.read函数的同步版本，返回bytesRead的个数          |
| fs.write(fd, buffer, offset, length[, position], callback) |           通过文件标识fd，向指定的文件中写入buffer           |
|    fs.write(fd, data[, position[, encoding]], callback)    | 把data写 入到文档中通过指定的fd,如果data不是buffer对象的实例，则会把值强制转化成一个字符串。 |
|    fs.writeSync(fd, buffer, offset, length[, position])    |                     fs.write()的同步版本                     |
|       fs.writeSync(fd, data[, position[, encoding])        |                      fs.write()的同步版                      |
|                   fs.close(fd, callback)                   |                      关闭一个打开的文件                      |
|                      fs.closeSync(fd)                      |                     fs.close()的同步版本                     |
|     fs.writeFlie(filename, data, [options], callback)      | 异步的将数据写入一个文件,如果文件不存在则新建,如果文件原先存在，会被替换。data可以是一个string，也可以是一个原生buffer。 |
|        fs.writeFileSync(filename, data, [options])         |     fs.writeFile的同步版本。注意：没有callback，也不需要     |
|     fs.appendFile(filename, data, [options], callback)     | 异步的将数据添加到一个文件的尾部，如果文件不存在，会创建一个新的文件。data可以是一个string，也可以是原生buffer。 |
|        fs.appendFileSync(filename, data, [options])        |                   fs.appendFile的同步版本                    |
|         fs.readFile(filename, [options], callback)         |                  异步读取一个文件的全部内容                  |
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

这个环节一般是建立在已经做好了一个网页，准备发布或者将要调试的前提下进行，那么在开始之前我们就要先了解网页的运作有哪些规则。

> ***举个🌰：***
>
> “网页”顾名思义，是基于网络进行运作的页面，那么计算机是如何与网页产生互动的？
>
> 计算机访问网页，可以想象成**查询字典的过程**。
>
> 回想一下查询字典的流程：我们**将某个字的部首或者偏旁信息**提供给目录，通过目录**找到对应的页数**，最后在某页找到自己想要的字。
>
> 建立网页的原理正好相反：对于网络这本“字典”，我们要将自己创造的”某一页“塞入这本字典。
>
> 因此需要提供**任何计算机都能够找到这一页**的数据，比如**是哪本字典“字典” — 主机名(`hostname`)、在哪一“页” — 端口号(`port`)** 等等。

先来看一个简单案例：


```js
//创建并返回一个HTTP服务器对象
let http = require('http');

//let server = http.createServer([requestListener])
//requestListener[可选]：监听到客户端连接的回调函数
let server = http.createServer();

//server服务启动后使用的事件
server.on("listening",function(){
    console.log("正在监听请求");
});

server.on("request",function(req,res){
    console.log("检测到客户端请求");
});

//监听客户端连接请求，只有当调用了listen方法以后，服务器才开始工作
//server.listen(port, [hostname], [backlog], [callback]);
//port：监听的端口
//hostname[可选]：主机名(IP/域名)
//backlog[可选]：连接等待队列的最大长度
//callback[可选]：调用listen方法并成功开启监听以后，会触发一个listening事件，callback将作为该事件的执行函数

server.listen(config.server.port, config.server.host);
```

计算机通过以上代码完成了这些事情：

1. 发送一个http请求到指定主机（`ip`）。
2. 服务器（`server`）接收请求（`request`）【即符合http协议的报文】，将报文进行拆解（提取信息）、处理。
3. 服务器处理完之后，返回给客户端想要的数据（响应response）。
4. 客户端（浏览器）接收到数据后，分析并处理

> 优先级：`http.Server < net.Server < EventEmitter`



### server.on事件

| 事件      | 描述                                               | 参数                                                         |
| :-------- | :------------------------------------------------- | :----------------------------------------------------------- |
| listening | 当server调用listen方法并成功开始监听以后触发的事件 |                                                              |
| error     | 当服务开启失败时触发的事件                         | `err`：具体的错误对象                                        |
| request   | 当有客户端发送请求到该主机和端口的请求时触发       | `req`：request对象，`http.IncomingMessage`的一个实例， 通过它我们可以获取到这次请求的一些信息：<li>`httpVersion`：使用的http协议的版本</li><li>`headers`：请求头信息中的数据</li><li>`url`：请求的地址</li><li>`method`：请求方式</li><br/>`res`：response对象，`http.ServerResponse`的一个实例，通过它我们可以向该次请求的客户端输出返回响应：<br/><li>`write(chunk, [encoding])`：发送一个数据块到响应正文中</li><li>`end([chunk], [encoding])`：当所有的正文和头信息发送完成以后调用该方法告诉服务器数据已经全部发送完成了，这个方法在每次完成信息发送以后必须调用，并且是最后调用</li><li>`statusCode`：该属性用来设置返回的状态码</li><li>`setHeader(name, value)`：设置返回头信息</li><li>`writeHead(statusCode, [reasonPhrase], [headers])`：这个方法只能在当前请求中使用一次，并且必须在response.end()之前调用</li> |



## url模块


使用fs模块实现nodejs代码和html的分离

```js
var url = require('url');
```

根据不同的url进行处理，返回不一样的数据：

- url.parse(request.url) ：对url格式的字符串进行解析，返回一个对象
- get请求的数据处理
- post请求的数据处理
  - post发送的数据会被写入缓冲区中，需要通过resquest的data事件和end事件来进行数据拼接处理
- querystring模块
  - parse()：将一个query string反序列化为一个对象



{% note [info] 当一个网站准备发布时 %}

- *需要考虑什么？*
  1. 源码的安全性
  2. 性能
- *如何进行优化？*
  1. **给代码瘦身：**删除换行、制表符、注释……
  2. **将代码“脏化” — 让读代码的人骂脏话：**变量抽象化、语法重构（升级）、语句合并……
- *代码合并方式有哪些？*
  1. 实时自动合并（`fs.watch`）
  2. 手动一次性合并

{% endnote %}