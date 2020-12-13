---
title: Unity 读取外部文件资源
tags: [游戏编程]
categories: 
- [Unity]
- [C#]
mathjax: true
date: 2020-12-11
---

在Unity编辑器中的资源路径有：

- `Application.dataPath`：对应于项目的Assets文件夹

- `Application.sreamingAssetsPath`：用于返回流数据的缓存目录，返回路径为相对路径，适合设置一些外部数据文件的路径

- `Application.persistentDatePath`：系统路径，适合存一些持久化的数据

- `Application.temporaryCatchPath`：系统路径，适合存一些临时数据的缓存目录



## 四种读取外部资源的方式

1. `Resources`文件夹

`Assets`文件夹下一个默认隐式的文件夹，如果用户新建了同名的文件夹，就会覆盖原来的。

这个文件夹只能动态读，不能动态写。**它会随着项目打包被压缩与加密。**适合存放一些prefab资源，因为它在打包的时候回自动删除一些用不到的prefab，有利于精简压缩包。

如果`Resources`文件夹下有一个`Text.xml`，那么读取方式为：

```c#
Resources.Load("Text");
```



2. `StreamingAssets`文件夹

同样是只读的保留文件夹。但是**在打包时不被压缩与加密，只会原封不动地打入包中。**适合存放二进制文件，只能用`WWW`类或`UnityWebRequest`来读取。

如果`StreamingAssets`文件夹下有一个`Text.xml`，那么读取方式为：

```c#
string path = Application.streamingAssetsPath + "/Text.xml";
WWW www = new WWW(path);
string s = www.text;
```

3. `AssetBundle`

**这个不是一个文件夹，而是一种二进制文件类型**，可以将prefab与二进制文件封装成`AssetBundle`文件，它不能更新脚本，但是如果prefab上挂的是本地脚本就能运行，只能用`www`类加载该文件类型。

将`Text.xml`打包生成`AssetBundle`文件`TextXML.bundle`，将这个二进制文件放入`StreamingAssets`文件夹中。读取方式为：

```c#
string path = Application.sreamingAssetsPath + "/TextXML.bundle";
WWW www = new WWW(path);
www = WWW.LoadFormCatchOrDownload(path,0);
AssetBundle ab = www.assetBundle;
//以上都是和一般的读取StreamingAssets中读取文件是一样的，就是多了一句www=WWW.LoadFormCatchOrDownload(path,0);
//然后通过读取的ab读取原text文件
string path = "text";
TextAsset text = ab.Load(path,typeof(TextAsset)) as TextAsset;//这个函数的语法和Resource一样
string s = text.ToString();
```

4. `PersistentDatePath`

运行时可读写，没运行时不能，无内容限制。

这个文件路径在`iOS`平台上是沙盒，在安卓平台上可以在`ProjectSetting`的`Write Access`中设置是沙盒还是`SDcard`。