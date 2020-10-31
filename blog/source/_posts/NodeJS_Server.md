---
title: Node.JS 创建本地服务器并显示html页面
tags: [编程,前端]
categories: 
- [JavaScript]
- [Node]
mathjax: true
date: 2020-10-17
---

> 前瞻阅读：[Node.JS大峡谷](https://msyqgzt.gitee.io/blogs/2020/09/24/NodeJS/)


## 准备工作

html页面得先有吧？不解释了吼~

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ah~hello啊</title>
</head>
<body>
    <h1>就™你在访问我啊？</h1>
</body>
</html>
```



准备一个index.js，跟index.html放在同一目录下。

- 导入需要用到的node自带模块

```js
const http = require("http"),//创建服务器需要http模块
      fs = require("fs"),    //fs模块用来读写html
      url = require("url");  //url模块用来解析输入的url
```

  这里`index.html`和`index.js`都放在一个目录(`项目名/html`)下的，属于同级。

## 创建服务器

1. 先来个简单版本

```js
const http = require("http"),//创建服务器需要http模块
      fs = require("fs"),    //fs模块用来读写html
      url = require("url");  //url模块用来解析输入的url

//创建服务器对象
let server = http.createServer();
//服务器监听时打印"listening"
server.on("listening", function () {
    console.log("listening");
})

//服务器接收到请求时
server.on("request", function (req, res) {
    //发送响应头请求（状态码，状态信息，响应头对象）
    res.writeHead(200, "OK", {
        'Content-Type': 'text/html'//将数据作为html对象进行编码
    });

    //读取html文件，并反馈到客户端
    fs.readFile('./html/index.html', 'utf8', function (err, data) {
        if (err) throw err;
        res.end(data);
    });
})

server.timeout = 5000;//5秒响应时间
server.listen(4000, 'localhost');//监听地址-localhost:4000
```

不出意外的话，运行脚本就可以访问了

{% asset_img 1.1.png 1.1 %}

---

2. 比较常用的版本
  
     `createServer`方法创建一个`sever`，每次请求从`request`拿到`url`，解析后找到对应文件，获取成功后写入`response`；失败则发送404.

  ```js
const http = require("http"),//创建服务器需要http模块
    fs = require("fs"),    //fs模块用来读写html
    url = require("url");  //url模块用来解析输入的url

//将createServer与server.on合并(req = request, res = response)
var server = http.createServer(function (req, res) {
    //将被请求的url解析为Json信息
    const urlJson = url.parse(req.url);

    //根据不同的请求地址显示不同的网页
    switch (urlJson.pathname) {
        case '/':
            res.writeHead(200, "OK", {
                'Content-Type': 'text/html'
            });
            readHtml(res, './html/index.html');
            break;

        case '/user':
            res.writeHead(200, "userPage", {
                'Content-Type': 'text/html'
            });
            readHtml(res, './html/userList.html');
            break;

        default:
            res.writeHead(404, "Err", {
                'Content-Type': 'text/html'
            });
            res.end("<h1>404 Error!</h1>");
            break;
    }
});

//专门读取文件的函数，将对应信息推送给客户端
function readHtml(res, path) {
    fs.readFile(path, 'utf8', function (err, data) {
        if (err) throw err;
        res.end(data);
    });
}

server.timeout = 5000;//5秒响应时间
server.listen(4000, 'localhost');//监听地址-localhost:4000
  ```

  
