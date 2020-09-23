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

#### 模块调用(require)

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



#### 模块数据(exports)

- module
  - 在Node中，每一个模块都有自己的作用域，同时还有一个module的变量，代表了对当前模块的引用，但module并不是全局对象，并且每个模块都有白己的独立的module对象
    - module.id
    - module.fileName
    - module.diyName
  - module.exports
  - exports===module.exports
- 外部模块通过`require()`方法加载模块，该函数返回的就是被加载模块的`module.exports`对象
- 我们可以通过`module.exports`或者`exports`对外提供模块内部变量的访问

注意的是最好不要直接覆盖`exports`或者`module.exports`，比如`exports=1` ,`module.exports=1`，这样做会破坏`exports`和`module .exports`的引用关系



## global对象

- `_filename`属性

  - 返回当前执行的文件的文件路径，该路径是经过解
    析后的*绝对路径*，在模块中，该路径是模块文件的
    路径，此属性并非全局属性，而是模块的

- `_dirname`属性
  - 返回当前执行脚本文件所在目录的路径，该属性也
    是模块的，而非全局
  
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
    
    - ```
      new Buffer(size);
      new Buffer(array);
      new Buffer(string,[encoding]);
      ```
    
    - | 属性 | 描述 |
      | :--: | :--: |
      |      |      |
      |      |      |
      |      |      |
      |      |      |
      |      |      |
      |      |      |
      |      |      |
      |      |      |
      |      |      |
    
      





