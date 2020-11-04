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

点击`LAN Host`，场景内就会出现一个立方体，代表一个`Player`进入了该服务器



#### 模拟多客户端效果

在引擎菜单选择`建立并运行`(`Build And Run`)，点击`LAN Host`创建服务器，同时在引擎中运行游戏，相当于两个客户端在同时运行。
在引擎中选择`LAN Client`，此时两个游戏端口都出现了两个立方体，若关闭作为服务器的端口，玩家们被踢出，游戏场景会被清空。



## 让角色在服务器动起来

### 改造一下脚本

将预制物体挂载`Network Transform`组件

- `Compress Rotation`用于控制服务器对于物体旋转的字节压缩，也可以选择无旋转
- `Network Sync Mode`用于控制服务器同步模式，默认是`Observes`观察者，每个人都将在网络中获得同步；或者选择`Owner`所有者，只有拥有玩家的端口获得网络同步。
- `Network Sync Interval`用于控制服务器的同步间隔

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
在引擎中选择`LAN Client`，此时游戏内出现了一个立方体，并且每秒会刷新位置。

**但是似乎会有些小问题，刷新的频率会比较奇怪**，这是因为脚本的继承类需要修改，将在下部分后面解决这个问题。



### 访问角色子物体

现在我们在`Cube`这个角色下新建一个子物体`Sphere`，将预制物体挂载`Network Transform Child`组件，属性大多与`Network Transform`组件一致，唯一多出一个`Target`属性，用于设置将要随之同步的子物体，我们把`Sphere`子物体赋予`Target`。

更改代码，添加子物体的随机位置

```c#
[SerializeField]
Transform _child;

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
        _child.localPosition = ReturnRandom();
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

建立本地服务器测试，游戏内出现了一个立方体和球体，并且每秒会刷新位置，但球体虽然是立方体的子对象，但它和立方体的相对位置并不是固定的，子物体能够脱离父物体的束缚来随机位置。



### 修改为正确的继承类

现在我们来解决之前提到的刷新频率问题

之前脚本的继承类是`MonoBehavior`，现在我们引用`Mirror`命名空间，并将脚本类继承到`NetworkBehavior`类，就像这样：

```c#
using Mirror;

public class RandomizePosition : NetworkBehavior
{
    //Codes
}
```

重新建立本地服务器，之前的刷新问题消失了，能够稳定地进行每秒一次的位置变换。