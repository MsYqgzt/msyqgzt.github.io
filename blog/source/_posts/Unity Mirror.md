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

## 基本网络环境的建立

在游戏场景内添加空物体，名为`Network Manager`，挂载以下脚本

- Network Manager - 游戏网络管理组件
  - `Network Info`
    - `Network Address`：服务器的ip地址，主机上的运行默认为`localhost`。
    - `Max Connections`：连接到服务器的最大客户端数量，即最大玩家数。
  - `Spawn Info`
    - `Player Prefab`：作为玩家代表的角色产生的预制体
    - `Auto Create Player`：玩家进入游戏场景时是否自动产生预制角色
    - `Registered Spawnable Prefabs`：可生成的预制体列表。在游戏场景中临时产生的物体，如子弹等模型，都需要作为预制体注册进这个列表
- Network Manager HUD
  - `LAN Host`：作为服务器，同时作为客户端登陆游戏场景。
  - `LAN Client IP`：仅作为客户端连接到指定IP地址的游戏场景。
  - `LAN Server Only`：仅作为服务器创建场景，不产生玩家。



### 创建一个简单服务器

一个多人游戏是如何构成的呢？多人游戏就是多个玩家在同一个游戏世界里，每个玩家都有自己的客户端进入这个世界。

那么首先就需要一个提供多个客户端数据传输的载体，也就是服务器。玩家通过客户端对服务器发起请求，最终在同一个游戏世界相遇。



#### 先造个玩家吧

作为入门的部分，我们以最简单的方式表达玩家。反手就是创建`Cube`，对其挂载一个简单的移动脚本，让它在X，Y轴上在(4,4)到(-4,-4)之间取随机位置。

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

> **脚本需要做的更改：**
>
> - `using Mirror`
> - 脚本继承类改为`NetworkBehaviour`（继承自`MonoBehaviour`）

玩家预制体内需要的脚本：

- `NetworkIdentity`：网络物体必备的脚本



#### 运行游戏

在编辑器内尝试运行游戏，会看到`HUD`的UI界面

点击`LAN Host`，场景内就会出现一个立方体，代表一个`Player`进入了该服务器



#### 模拟多客户端效果

在引擎菜单选择`建立并运行`(`Build And Run`)，点击`LAN Host`创建服务器，同时在引擎中运行游戏，相当于两个客户端在同时运行。
在引擎中选择`LAN Client`，此时两个游戏端口都出现了两个立方体，若关闭作为服务器的端口，玩家们被踢出，游戏场景会被清空。



## 多人游戏中玩家的移动控制

### 改造一下脚本

将预制物体挂载`Network Transform`组件

- `Compress Rotation`用于控制服务器对于物体旋转的字节压缩，也可以选择无旋转
- `Network Sync Mode`用于控制服务器同步模式，默认是`Observes`观察者，每个人都将在网络中获得同步；或者选择`Owner`所有者，只有拥有玩家的端口获得网络同步。
- `Client Authority`：客户端玩家是否获得服务器授权。若要**在客户端控制角色并同步到服务器**，需要勾选此项。
- `Network Sync Interval`：用于控制服务器的同步间隔

记得最初的玩家脚本吗？当初我们让角色在进入服务器的时候产生在某个范围内的随机位置上，现在让它稍微复杂一点。

我们要让它在服务器运行的时候，每隔一段时间就进行位置改变，范围同样是之前的随即范围。因此单独分一个函数进行随机，使用协程实现时间间隔，从而实现位置变换。

```c#
void Start(){
	StartCoroutine(__RandomizePosition());
}

IEnumerator __RandomizePosition)()
{
    WaitForSeconds wait = new WaitForSeconds(1f);
    Vector3 startPosition = transform.position;
    
    while (true)
    {
        transform.position = startPosition + ReturnRandom();
        yield return wait;
    }
}

Vector3 ReturnRandom()
{
    return new Vector3(
		Random.Range(-4f,4f),
		Random.Range(-4f,4f),
		0f);
}
```

在引擎菜单选择`建立并运行`(`Build And Run`)，点击`LAN Server Only`创建空服务器，同时在引擎中运行游戏。
在引擎中选择`LAN Client`，此时游戏内出现了一个立方体，并且进行每秒一次的位置变换。