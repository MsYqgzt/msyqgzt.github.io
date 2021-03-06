---
title: C# 移位操作符"<<" & ">>"
tags: [编程]
categories: 
- [C#]
mathjax: true
date: 2020-11-14
---



移位操作符是将二进制数据进行的操作

## 左移操作符(<<)

举个🌰，十进制数2转化成二进制数是10，若将2的二进制数左移1个操作符，即：
$$
\begin{split}
2 << 1 &=> 10_{(2)} << 1\\\\
&=100_{(2)}\\\\
&=4
\end{split}
$$


将第一个操作数向左移动第二个操作数指定的位数，空出的位置补0。
**左移相当于乘。**左移1位相当于乘2；左移2位相当于乘4；左移3位相当于乘8……左移n位相当于乘$2^n$

```c#
x << 1 = x * 2;
x << 2 = x * 4;
x << 3 = x * 8;
x << 4 = x * 16;
```



## 右移操作符(>>)

右移位运算符（>>）是把数向右移位，所有的位都向右移动指定的次数
$$
\begin{split}
4 >> 1 &=> 100_{(2)} >> 1\\\\
&=10_{(2)}\\\\
&=2
\end{split}
$$
**右移相当于除**。右移1位相当于除2；右移2位相当于除以4；右移3位相当于除以8……右移n位相当于除以$2^n$，然后**取其整数**。

```c#
x >> 1 = (int)(x / 2);
x >> 2 = (int)(x / 4);
x >> 3 = (int)(x / 8);
x >> 4 = (int)(x / 16);
```



变量的赋值移位运算可简写：

```c#
x = x << 1;
y = y >> 2;
//等同于
x <<= 1;
y >>= 2;
```

