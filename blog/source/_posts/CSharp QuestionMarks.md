---
title: C#中?、??与?:的使用
tags: [编程]
categories: 
- [C#]
mathjax: true
date: 2020-11-15
---



今天研究别人的轮子，遇到了很有趣的代码。

```c#
 backGround.position = eventData.pressEventCamera?.ScreenToWorldPoint(pointerDownPosition) ?? pointerDownPosition;
```

研究了一下，决定记录并整理下类似符号的用法。



## 可空类型修饰符（?）

被`?`修饰的变量类型可以是空值`null`

```c#
int? a = null;
```



## 空合并运算符(??)

用于定义可空类型和引用类型的默认值。

如果此运算符的**左操作数不为null**，则此运算符将**返回左操作数，否则返回右操作数**。

例如：a ?? b，当`a=null`时返回b，`a!=null`时则返回a本身。

```c#
string a = null;
string b = "b";
string c = "c";
var d = a ?? b ?? c;  //"b"
```



## 三元（运算符）表达式（?:)

`x?y:z`表示**如果表达式x为true，则返回y；如果x为false，则返回z**，是省略if{}else{}的简单形式。

```c#
x?y:z;//等同于：
if(x) y
else z
```



## 举个🌰

一般这些符号的使用有助于提高代码的健壮性。

比如：*在不报异常的情况下*，取为`null`的`List`集合的个数

```c#
List<string> list = null;
var a = list?.Count ?? 0;//b = 0
var b = list == null ? 0 : list.Count;//b = 0
```

