---
title: UnityEvent 实现事件监听(Listener)
tags: [游戏编程]
categories: 
- [Unity]
- [C#]
mathjax: true
date: 2020-12-02
---



在Unity中会遇到很多需要检测某个数据来触发某些代码的场景。

例如：

- 角色升级 — 检测角色等级变量是否改变，是则触发一系列提升玩家属性的代码

- 实现类似的`OnValueChanged`事件等等

这些功能大多数都可以通过`Update()`函数来做，但是吧……这样的代码一但多起来，看起来就会*又臭又硬.jpg*

这些行为可以通过事件注册与添加事件监听的方法来*优雅地*实现

## 初次见面

Unity内有一个支持泛型参数的类`UnityEvent<>`，它支持注册任意数量和任意数据类型的事件。

举个🌰，假设我需要做一个秒表计时器，计时的结果存储在一个整数变量`time`中，通过一个协程让它每秒+1

基础功能代码如下：

```c#
void Start()
{
    int time = 0;
    StartCoroutine(PrintString(time));//开启协程
}

IEnumerator PrintString(int time)
{
    time++;
    yield return new WaitForSeconds(1);//等待1秒
    StartCoroutine(PrintString(time));//循环自身
}
```



## 注册事件

好了，现在我们有一个计时器，但是我不知道它在某一时间计时到第几秒

现在假设我需要它**在其他的功能脚本中**每五秒就获取一次计时器的值并且打印出来。这明显需要在类之间调用数据，一般会想到将`time`这个局部变量设置为公有变量。

那么重点来了，现在我们换一个思路，**为`time`注册一个整数类型的事件**

```c#
//定义一个事件类，继承自UnityEvent，事件类包含一个整数类型
public class TimeEvent : UnityEvent<int> { };
TimeEvent OnTimeChanged = new TimeEvent();//注册事件
```



## 添加回调与监听

注册了事件还不够，我们需要在`time`值改变的代码位置做一些手脚，让它**值改变时发出一个信号，并且有监听器接收**。

这里的信号就是`Invoke`，监听器即在刚刚注册的事件上调用`AddListener()`

```c#
public class Test : MonoBehaviour
{
	//定义一个事件类，继承自UnityEvent，事件类包含一个整数类型
	public class TimeEvent : UnityEvent<int> { };
	TimeEvent OnTimeChanged = new TimeEvent();//时间刷新事件

    void Start()
    {
        int time = 0;
		
        //lambda事件监听，接收Invoke
        OnTimeChanged.AddListener(t =>
        {
            print("目前计时:" + t);
        });

        StartCoroutine(PrintString(time));
    }

    IEnumerator PrintString(int time)
    {
        time++;
        OnTimeChanged.Invoke(time);//监听回调，传给所有Listener
        yield return new WaitForSeconds(1);
        StartCoroutine(PrintString(time));
    }
}
```



如此便实现了自定义事件的实现，并且跨类的调用也很方便。

```c#
public class Test1 : MonoBehaviour
{
    
    void Start()
    {
		//读秒
        OnTimeChanged.AddListener(t =>
        {
            print("目前计时:" + t);
        });
        //读分钟
        OnTimeChanged.AddListener(t =>
        {
            if(t % 60 == 0)
            print("分钟:" + (t / 60));
        });
    }
```

