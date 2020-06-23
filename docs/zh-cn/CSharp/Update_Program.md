# WPF制作在线升级程序

> 制作流程：
>
> 1. 使用某服务商的“对象存储“服务
> 2. 编辑更新内容文档

## 编辑更新内容文档

新建一个`.txt`或`.log`文档，写入新版本要显示的信息

```
Version:1.1.0
更新时间:2020-06-11

「更新内容」
（这里是更新内容）
```

这个文件最终要上传到

## 设定程序版本号

在`app.config`中加入版本号信息

```xaml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <appSettings>
    <!-- 程序版本号 -->
    <add key="MainVersion" value="1.0.0"/>
    <!-- 在线程序版本信息的文件链接 -->
    <add key="UpdateLog" value="http://....../update.log"/>
  </appSettings>
</configuration>
```

