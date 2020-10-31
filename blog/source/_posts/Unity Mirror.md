---
title: Unity 使用Mirror框架制作多人游戏
tags: [游戏编程,游戏引擎,C#]
categories: 
- [Unity]
- [游戏]
mathjax: true
date: 2020-10-21
---

## Mirror入门

> Mirror是uMMORPG、vival和Cubica的开发人员为MMO规模的网络而构建和测试的。Mirror使联网变得容易，简洁和可维护。使用少于6000行代码即可实现uMMORPG。我们只需要一个网络库来启动我们的游戏就可以了。

### 准备工作

1. Unity Assets Store内下载[Mirror](https://assetstore.unity.com/packages/tools/network/mirror-129321)，导入后重启Unity
2. 确保游戏版本基于`.NET Framework 4.x` 以上 
3. 创建一个空对象(`GameObject`)，命名为Network Manager，添加以下组件
   - NetWork Manager
   - Network Manager HUD
   - Network Identity
     - 为了使多维数据存在，必须有这个网络组件，勾选它的`Local Player Authority`，以后的版本可能合并这个选项，如果没有看见这个选项，不用担心，继续下面的步骤

### 创建一个简单服务器

一个多人游戏是如何构成的呢？多人游戏就是多个玩家在同一个游戏世界里，每个玩家都有自己的客户端进入这个世界。

那么首先就需要一个提供多个客户端数据传输的载体，也就是服务器。玩家通过客户端对服务器发起请求，最终在同一个游戏世界相遇。



#### 先造个玩家吧

作为入门的部分，用啥当玩家重要吗？

反手就是创建`Cube`，对其挂载一个简单的移动脚本，让它在X，Y轴上在(4,4)到(-4,-4)之间取随机位置。

```c#
void Start(){
	transform.position += new Vector3(
		Radom.Range(-4f,4f),
		Radom.Range(-4f,4f),
		0f);
}
```

接着把挂载完成的游戏对象保存为预制体(`Prefabs`)

将预制体赋予Network Manager脚本中的`Player Prefab`属性，并确保`Auto Create Player`选项处于勾选状态。



#### 运行游戏

在编辑器内尝试运行游戏，会看到一套UI
- `LAN Host`：创建一个服务器，同时作为客户端加入服务器。这将在创建服务器的同时产生一个玩家。
- `LAN Client`：仅作为客户端加入指定IP地址。服务器中将产生一玩家
- `LAN Server Only`：仅作为服务器，不产生玩家。

点击`LAN Host`，场景内就会出现一个立方体，代表一个Player进入了该服务器



#### 模拟多客户端效果

在引擎菜单选择`建立并运行`(`Build And Run`)，点击`LAN Host`创建服务器，同时在引擎中运行游戏，相当于两个客户端在同时运行。
在引擎中选择`LAN Client`，此时两个游戏端口都出现了两个立方体，若关闭作为服务器的端口，玩家们被踢出，游戏场景会被清空。