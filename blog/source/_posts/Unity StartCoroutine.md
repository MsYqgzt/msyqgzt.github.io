---
title: Unity StartCoroutine 与 yield return 深入研究
tags: [游戏编程,游戏引擎,C#]
categories: 
- [Unity]
- [游戏]
mathjax: true
date: 2020-10-19
---

## 初次见面

字面意思很好理解，`StartCoroutine`就是开启一个协程，`yield return`是迭代器块返回调用迭代的地方。

先看一下Unity官方的解释

> **<span style="color:royalBlue">MonoBehaviour</span>.StartCoroutine**
>
> Coroutine StartCoroutine(<span style="color: LightSeaGreen">lEnumerator</span> routine);
>
> Starts a coroutine. The execution of a coroutine can be paused at any point using the yield statement. The yield return value specifies when the coroutine is resumed. Coroutines are excellent when modelling behaviour over several frames. Coroutines have virtually no performance overhead. StartCoroutine function always returns immediately, however you can yield the result. This will wait until the coroutine has finished execution.
>
> <br/>
>
> > 翻译:
> >
> > 一个协程的执行可以在任何地方用`yield`语句来暂停，`yield return`的值决定了什么时候协程恢复执行。协程在协调在几帧中执行的操作时有极大的用处.协程几乎没有任何性能开销。`StartCoroutine`一般都会立即返回，然而你也可以获得返回结果的值。但是这一步会等到协程结束执行才能生效。

<br/>

根据这个意思，我们来举个🌰

```c#
using System.Collections;
using UnityEngine;

public class Test : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        Debug.Log("start1");
        StartCoroutine(TestFunc());
        Debug.Log("start2");
    }

    IEnumerator TestFunc()
    {
        Debug.Log("test1");
        yield return null;
        Debug.Log("test2");
    }
}
```

> 运行结果：
>
> start1
>
> test1
>
> start2
>
> test2

当`StartCoroutine`刚调用的时候，可以理解为正常的函数调用，然后接着看调用的函数里面。

当被调用函数执行到`yield return null；`（暂停协程，等待下一帧继续执行）时，根据解释，协同程序会被暂停，先返回开始协程的地方，然后再暂停协程。也就是先通知调用处，“你先走吧，不用管我”，然后再暂停协程。

再来个🌰验证一下。

```c#
using System.Collections;
using UnityEngine;

public class Test : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        Debug.Log("Start1");
        StartCoroutine(TestFunc());
        Debug.Log("Start2");
    }

    IEnumerator TestFunc()
    {
        Debug.Log("Test1");
        yield return new WaitForSeconds(3);
        Debug.Log("Test2");
    }
}
```

> 运行结果：
>
> start1
>
> test1
>
> start2
>
> test2（三秒后显示）

验证了 "`yield return`的值决定了什么时候协程恢复执行" 这个特点。





## 食用指南

### 启动协同程序

协同程序有不同的启动方式：

1. `StartCoroutine(IEnumerator routine);`

```c#
StartCoroutine(Example());//注意方法名后加括号，参数可写在括号里
```

  - 优点：灵活，性能开销小。
  - 缺点：无法单独的停止这个协程，如果需要停止这个协程只能等待协同程序运行完毕或则使用`StopAllCoroutine();`方法。



2. `StartCoroutine (methodName:string, value : object = null);`

```c#
StartCoroutine("string methodName",value);//注意双引号，value为想传递的参数
```

  - 优点：可以直接通过传入协同程序的方法名来停止这个协程：`StopCoroutine("string methodName");`(注意双引号)
  - 缺点：性能的开销较大，只能传递一个参数。



### 停止协同程序

1、`StopCoroutine(string methodName);`

2、`StopAllCoroutine();`

3、设置`gameObject`的`active`为`false`时可以终止协同程序，但是再次设置为true后协程不会再启动。





### 协同程序的执行顺序

开始协同程序 -> 执行协同程序 -> 中断协同程序（中断指令）-> 返回上层继续执行 -> 中断指令结束后，继续执行协同程序剩下的内容





### 协同程序的注意事项

1. **不能在`Update()`或者`FixUpdate()`方法中使用协同程序**，否则会报错。

2. 关于中断指令：`YieldInstruction`，一个协程收到中断指令后暂停执行，返回上层执行同时等待这个指令达成后继续执行。





#### 协程中断指令

|        指令        |          描述          |                     实现                      |
| :----------------: | :--------------------: | :-------------------------------------------: |
|   WaitForSeconds   |      等待指定秒数      |      yield return new WaitForSeconds(2);      |
| WaitForFixedUpdate |     等待一个固定帧     |    yield return new WaitForFixedUpdate();     |
| WaitForEndOfFrame  |       等待帧结束       |     yield return new WaitForEndOfFrame();     |
|   StartCoroutine   | 等待一个新协程**结束** | yield return StartCoroutine(other coroutine); |

