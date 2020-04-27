# 代码实现贝塞尔曲线

贝塞尔曲线是我在学习过程中发现的很有意思的一个玩意儿。这次就打算用C#实现一下它的案例。

首先我们先来大致了解一下什么是贝塞尔曲线

>在进行汽车外形设计时，先用折线段勾画出汽车的外形*大致轮廓*，然后用光滑的参数曲线去**逼近**这个折线多边形
>
>这个折线多边形被称为**特征多边形**。
>
>逼近该特征多边形的曲线被称为**Bezier曲线**。

um，大致理解的意思就是在直线图像的基础上创造曲线，那么先有直线 再有曲线 这个思路就明朗了。

我们要做的就是**自己画几条连续的直线段**，最后的产生曲线交给算法即可。

## 编写画直线功能

C#里的画直线其实不难，其实就是…………[断片]

~~百度以后得知~~，在控件内绘制直线，实际上就三个东西：*起始点、末尾点和一支笔*

起始点和末尾点的这个‘点‘，C#内提供了一个自带的类封装`Point`

我们来看一下对于这个类的定义

```c#
struct System.Drawing.Point{
    public Point(int x, int y);
}
```

可以看出，C#中有很多点的表示形式，其中就有二维的坐标表达法

那么好了，现在我们有了定义点的方式，现在就需要再深入一点，这些点的数量我们不应该去限制（是可以这么干，但这不符合实际需求，总不能为了写1万个案例改1万次代码不是？）

所以我们需要一个能够“收放自如”的点的集合或者数组，它的容量能随着数据的数量而弹性改变。C#中还是有这么一样东西，叫做`List`，查查定义

> List泛型集合是C#编程中的经常使用的集合之一，相对数组，**它可以动态的添加元素而不是声明的时候就必须指定大小**。
>
> 相对于`ArrayList`集合和`Hashtable`集合的优势是其元素的数据类型可以确定。而不是默认的父类类型`object`。

什么泛型啊什么集合的，看不懂都无所谓，能够确定的是，它可以动态的添加元素，并且大小随之改变。

来看看这个东西的定义格式

```c#
using System.Collections.Generic;

List<数据类型> 变量名 = new List<数据类型>();
```

数据类型在这里应该就是`Point`了，这个类似数组的属性肯定会存在类似数组长度和索引位置的属性，我们稍后再探索。

定义直线的点坐标集合，同时曲线也是由像素点绘制成的，顺便一起定义产生的曲线中所有的点坐标集合

```c#
using System.Collections.Generic;
//直线的点坐标集合
List<Point> Points = new List<Point>();
//结果曲线中所有点的坐标集合
List<Point> resultPoints = new List<Point>();
```

------

现在我们有了点集合，就可以拿“笔”来画“形状”了，很不巧的是 C#中也有关于画笔的定义。

```c#
using System.Drawing;

Graphics gra = Paper.CreateGraphics();
Pen blackPen = new Pen(Color.Black, 2);//创建画笔（颜色，宽度/粗细）
gra.DrawLine(blackPen, 起点, 终点);//绘制直线
gra.DrawEllipse (blackPen, 左上角坐标X, 左上角坐标Y, 宽度, 高度);//绘制椭圆
```

画直线还有一步，就是要一个能画线的控件，这里我选择的是图片控件`PictureBox`，取名为`Paper`，也是有其他选择的，原理相同。

然后就需要对`Paper`的`Click`事件进行记录，将点击的点存入`List<Point>`集合中，至于坐标的获取，可以通过事件自带的`EventArgs`内获得。

好了，现在我们把代码组合起来，穿插空格来分清步骤：

```C#
private void Paper_Click (object sender, EventArgs e) {
    //第一步：标出点的位置
    Graphics gra = ((PictureBox)sender).CreateGraphics();
    Pen pen = new Pen(Color.Red, 2); //画笔（红色，2宽度）
    //画椭圆（x坐标、y坐标、宽、高）
    gra.DrawEllipse(pen, ((MouseEventArgs) e).X - 2, ((MouseEventArgs) e).Y - 2, 4, 4);

    //第二步：获取光标点击的坐标相对位置，存储坐标到List<Point>内
    Points.Add(new Point(((MouseEventArgs) e).X, ((MouseEventArgs) e).Y));

    
    //第三步：获取上一个点和当前点进行连线
    if (Points.Count >= 1) {
        Point point0 = new Point(Points[Points.Count - 1].X, Points[Points.Count - 1].Y);
        Point point1 = new Point(Points[Points.Count].X, Points[Points.Count].Y);
        gra.DrawLine(pen, point0, point1);
    }
}
```

到这一步，就可以画出华丽（?）的折线，也就是**特征多边形**了

![b0](https://gitee.com/MsYqgzt/picStorage/raw/master/Besier_0.gif)

## 画出贝塞尔曲线

终于到了重头戏，我们得找找一下贝塞尔曲线算法的规律

定义是其次，首先看一下计算方式

> **一次Bezier曲线**
>
> 当n=1时，有两个控制点$p_0$和$p_1$，Bezier多项式是一次多项式：
> $$
> p(t)=\displaystyle\sum_{i=0}^1a_if_{i,1}(t)
> =P_0B_{0,1}(t)+P_1B_{1,1}(t)
> $$
>
> $$
> \begin{cases}
> B_{0,1}(t)={1!\over 0!(1-0)!}t^0(1-t)^{1-0}=1-t\\
> B_{1,1}(t)={1!\over 1!(1-1)!}t^1(1-t)^{1-1}=t
> \end{cases}
> $$
>
> 因此：
> $$
> p(t)=\displaystyle\sum_{i=0}^1a_if_{i,1}(t)
> =P_0B_{0,1}(t)+P_1B_{1,1}(t)\\
> =(1-t)P_0+tP_1
> $$
> 这恰好是连接起点$P_0$和终点$P_1$的直线段
>
> <br/>
>
> **二次Bezier曲线**
>
> 当n=2时，有3个控制点$p_0、p_1和p_2$，Bezier多项式是二次多项式：
> $$
> p(t)=\displaystyle\sum_{i=0}^2a_if_{i,2}(t)
> =P_0B_{0,2}(t)+P_1B_{1,2}(t)+P_2B_{2,2}(t)
> $$
>
> $$
> \begin{cases}
> B_{0,2}(t)={2!\over 0!(2-0)!}t^0(1-t)^{2-0}=(1-t)^2\\
> B_{1,2}(t)={2!\over 1!(2-1)!}t^1(1-t)^{2-1}=2t(1-t)\\
> B_{2,2}(t)={2!\over 2!(2-2)!}t^2(1-t)^{2-2}=t^2
> \end{cases}
> $$
>
> 因此：
> $$
> p(t)=\displaystyle\sum_{i=0}^2a_if_{i,2}(t)
> =P_0B_{0,2}(t)+P_1B_{1,2}(t)+P_2B_{2,2}(t)\\
> =(1-t)^2P_0+2t(1-t)P_1+t^2P_2\\
> =(P_2-2P_1+P_0)t^2+2(P_1-P_0)t+P_0
> $$
>
> <br/>
>
> **三次Bezier曲线**
>
> 由4个控制点生成，这时n=3，有4个控制点$p_0、p_1、p_2$和$p_3$，Bezier多项式是三次多项式：
> $$
> p(t)=\displaystyle\sum_{i=0}^3a_if_{i,3}(t)=P_0B_{0,3}(t)+P_1B_{1,3}(t)+P_2B_{2,3}(t)+P_3B_{3,3}(t)
> $$
>
> $$
> \begin{cases}
> B_{0,3}(t)={3!\over 0!(3-0)!}t^0(1-t)^{3-0}=(1-t)^3\\
> B_{1,3}(t)={3!\over 1!(3-1)!}t^1(1-t)^{3-1}=3t(1-t)^2\\
> B_{2,3}(t)={3!\over 2!(3-2)!}t^2(1-t)^{3-2}=3t^2(1-t)\\
> B_{3,3}(t)={3!\over 3!(3-3)!}t^3(1-t)^{3-3}=t^3
> \end{cases}，为三次Bezier曲线的基函数
> $$
>
> 因此：
> $$
> p(t)=\displaystyle\sum_{i=0}^3a_if_{i,3}(t)
> =(1-t^3)P_0+3t(1-t)^2P_1+3t^2(1-t)P_2+t^3P_3
> $$
> 这四条基函数曲线均是三次曲线，任何三次Bezier曲线都是这四条曲线的线形组合

乍一看，似乎感觉一个头十个大，不过一定有共同的特征，我们把注意力放在它的**基函数**上
$$
一次贝塞尔曲线基函数\begin{cases}
B_{0,1}(t)={1!\over 0!(1-0)!}t^0(1-t)^{1-0}\\
B_{1,1}(t)={1!\over 1!(1-1)!}t^1(1-t)^{1-1}
\end{cases}，2个控制点\\
-\\
二次贝塞尔曲线基函数\begin{cases}
B_{0,2}(t)={2!\over 0!(2-0)!}t^0(1-t)^{2-0}\\
B_{1,2}(t)={2!\over 1!(2-1)!}t^1(1-t)^{2-1}\\
B_{2,2}(t)={2!\over 2!(2-2)!}t^2(1-t)^{2-2}
\end{cases}，3个控制点\\
-\\
三次贝塞尔曲线基函数\begin{cases}
B_{0,3}(t)={3!\over 0!(3-0)!}t^0(1-t)^{3-0}\\
B_{1,3}(t)={3!\over 1!(3-1)!}t^1(1-t)^{3-1}\\
B_{2,3}(t)={3!\over 2!(3-2)!}t^2(1-t)^{3-2}\\
B_{3,3}(t)={3!\over 3!(3-3)!}t^3(1-t)^{3-3}
\end{cases}，4个控制点
$$
这里就能发现整个基函数的形成规律！

对于任意次数的贝塞尔曲线，就会有**n个点**，有一个循环的参数**从0循环到n-1，我们可以把这个参数设为m**，循环结束后产生的**n个基函数**就得出来了。

而循环的内容是同一个公式，也就是：$B_{m,n}(t)={n!\over m!(n-m)!}t^m(1-t)^{n-m}$

这里基函数的结果可以用数组B[]来存储，大小**与控制点的数量一致**

<br/>

其中阶乘的部分我们可以单独编写一个函数，从1累乘到n，若输入为0，则返回结果为1。

```c#
//阶乘
int calculateN(int n) {
    if (n == 0) return 1;
    
    int result = 1;
    for (int i = 1; i < n; i++) 
        result *= i;
    return result;
}
```

那么就可以将基函数的运算翻译成代码，但是注意，这里还有一个**参数t**没有定义：

```c#
int m = Points.Count - 1;
double[] B = new double[Points.Count];

//计算所有基函数，分别累加XY，存到resultPoints
for (int i = 0; i < Points.Count; i++) {
    int temp = m - i;
    B[i] = calculateN (m) / (calculateN (i) * calculateN (temp)) * Math.Pow (t, i) * Math.Pow (1 - t, temp);
    X += (int)(Points[i].X * B[i]);
    Y += (int)(Points[i].Y * B[i]);
}
resultPoints.Add(new Point (X, Y));
```

<br/>

现在我们再回来收拾t这个家伙，这玩意代表着什么呢？

我们来看一个**参数化**的例子

给定3个点$p_0、p_1、p_2$，坐标分别是$(0,0)、(100,50)、(200,0)$，求一条2次的多项式曲线来插值这三个点。

解决方案是：假设第一个点参数$t=0$，第二个点参数取$t={1\over 2}$，第三个点的参数取$t=1$。

即$t_1=0,t_2={1\over 2},t_3=1$，带入参数方程$\begin{cases}x(t)=a_1t^2+a_2t+a_3\\y(t)=b_1t^2+b_2t+b_3\end{cases}$，随后得到6个方程，对应6个未知数就可以解出所有的未知数。

> **为何取参数$t_1=0,t_2={1\over 2},t_3=1$？**因为要使参数更接近/符合点的分布。
>
> 参数化的本质是找一组恰当的参数t来匹配这一组不同的**型值点**。给定一组不同的型值点，就要给出不同的参数化，即不同的t值，这样才使得这条曲线美观、合理。

怎么理解呢？起点是0，终点是1，二分点是1/2，正是**点的位置分布比例**！

那么是不是可以这么理解，t的增加量能体现曲线的光滑程度和精确度？

实践出真知嘛，我们把前一个代码块套上一个大的循环，让t从0开始不断增加到1

```c#
int n = Points.Count - 1;
double[] B = new double[Points.Count];

//t-绘制进度 & Δt-平滑程度
for (double t = 0; t < 1; t += 0.1) {

    int X = 0, Y = 0;
    
    //计算所有基函数，分别累加XY，存到resultPoints
    for (int i = 0; i < Points.Count; i++) {
        int temp = n - i;
        B[i] = calculateN (n) / (calculateN (i) * calculateN (temp)) * Math.Pow (t, i) * Math.Pow (1 - t, temp);
        X += (int) (Points[i].X * B[i]);
        Y += (int) (Points[i].Y * B[i]);
    }
    
    resultPoints.Add (new Point (X, Y));
    
}
```

算法的部分应该成型了，接下来跟上绘制的代码，塞入函数中，看看整体能否产生正常的曲线

```c#
using System;
using System.Collections.Generic;
using System.Drawing;
using System.Windows.Forms;

List<Point> Points = new List<Point>();
List<Point> resultPoints = new List<Point>();

//画板点击事件，添加控制点
private void Paper_Click(object sender, EventArgs e) {
    //第一步：标出点的位置
    Graphics gra = ((PictureBox)sender).CreateGraphics();
    Pen pen = new Pen (Color.Red, 2); //画笔（红色，2宽度）
    //画椭圆（x坐标、y坐标、宽、高）
    gra.DrawEllipse (pen, ((MouseEventArgs) e).X - 2, ((MouseEventArgs) e).Y - 2, 4, 4);

    //第二步：获取光标点击的坐标相对位置，存储坐标到List<Point>内
    Points.Add (new Point(((MouseEventArgs) e).X, ((MouseEventArgs) e).Y));
    
    //第三步：获取上一个点和当前点进行连线
    if (Points.Count > 1) {
        Point point0 = new Point(Points[Points.Count - 2].X, Points[Points.Count - 2].Y);
        Point point1 = new Point(Points[Points.Count - 1].X, Points[Points.Count - 1].Y);
        gra.DrawLine(pen, point0, point1);
    }
}

//阶乘
int calculateN(int n) {
    if (n == 0) return 1;
    
    int result = 1;
    for (int i = 1; i < n; i++) 
        result *= i;
    return result;
}

//曲线生成按钮点击事件
private void btnGen_Click(object sender, EventArgs e) {
    //=======贝塞尔曲线算法========
    int n = Points.Count - 1;
    double[] B = new double[Points.Count];
    //t-绘制进度 & Δt-平滑程度
    for (double t = 0; t < 1; t += 0.1) {
        int X = 0, Y = 0;

        //计算所有基函数，分别累加XY，存到resultPoints
        for (int i = 0; i < Points.Count; i++) {
            int temp = n - i;
            B[i] = calculateN (n) / (calculateN (i) * calculateN (temp)) * Math.Pow (t, i) * Math.Pow (1 - t, temp);
            X += (int) (Points[i].X * B[i]);
            Y += (int) (Points[i].Y * B[i]);
        }
        resultPoints.Add (new Point (X, Y));
    }
    //============================

    //绘制
    Graphics gra = Paper.CreateGraphics ();
    Pen blackPen = new Pen (Color.Black, 2); //创建画笔
    //循环所有结果点，顺次连接成直线
    for (int index = 1; index < resultPoints.Count; index++) {
        Point point0 = new Point (resultPoints[index - 1].X, resultPoints[index - 1].Y);
        Point point1 = new Point (resultPoints[index].X, resultPoints[index].Y);
        gra.DrawLine (blackPen, point0, point1);
    }
}
```

来看看最终效果

![b1](https://gitee.com/MsYqgzt/picStorage/raw/master/Besier_1.gif)

看来是成功的对吧！只是看起来不平滑。但还记得那个t吗，当初我们的精度是0.1，我们再缩小跨度，改成0.001，就可以纵享丝滑了。

![b2](https://gitee.com/MsYqgzt/picStorage/raw/master/Besier_2.gif)

不过这个算法从定义出发，还有一些小问题，运算效率也很离谱。



## 算法优化

贝塞尔曲线的另一种算法比较精髓，让我们来看看描述

> Bezier曲线的递推(de Castel jau)算法
>
> - 根据Bezier曲线的定义确定的参数方程绘制Bezier曲线，因其计算量过大，不太适合在工程上使用
>
> - Bezier曲线上的任一个点P(t)，都是**其它相邻线段的同等比例(t)点处的连线**，再取同等比例(t)的点再连线，一直取到**最后那条线段的同等比例(t)处**，该点就是Beizer曲线上的点P(t)
>
> - 由(n+1)个控制点$P_i(i=0,1,…,n)$定义的n次Bezier曲线$P_0^n$。n可被定义为分别由前、后n个控制点定义的两条(n-1)次Bezier曲线$P_0^{n-1}$与$P_1^{n-1}$的线性组合
>
> - 由此得到Bezier曲线的递推计算公式：
>   $$
>   P_i^k=\begin{cases}
>   P_i~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~k=0\\
>   (1-t)P_i^{k-1}+tP_{i+1}^{k-1}~~~~~~~~k=1,2,…,n~~/~~i=0,1,…,n-k
>   \end{cases}
>   $$

文字比较抽象，可以用一张图来描述这个过程，是最简单的三点曲线，更多点的情况下，道理是一样的。

![Bezier_Gen](https://gitee.com/MsYqgzt/picStorage/raw/master/Bezier%20gen.gif)

直观的来看还挺容易的，不过写起来可不一定，来理清一下思路

首先整个环境需要一个t∈[0,1]来作为大的循环前提。在这之下，提取每个线段的t比例的位置，得到一组新的直线段，再提取每个线段的t比例的位置，得到下一组直线段……直到最后只剩下2个点，也就是一个直线段的时候，提取这个直线段的长度在t比例下的位置，就获得了Bezier曲线在t比例下的像素坐标点了。

所以这里需要用到递归函数，会稍微复杂一些【也就秃顶了而已】，但是省去了阶乘和大量的四则运算，计算的效率有了质的飞跃。这里直接贴上核心算法的代码。

```c#
/// <summary> Bezier曲线算法 </summary>
/// <param name="points">特征多边形的点集合</param>
/// <param name="step">曲线平滑程度，数值越大越平滑</param>
/// <returns></returns>
List<Point> Bezier_generate(List<Point> points, int step = 100) {
    if (points.Count == 0) return new List<Point>();

    List<Point> resultPoints = new List<Point>();
    for (double t = 0; t < 1; t += 1.0 / step) {
        resultPoints.Add (Bezier_loop (points, new List<Point>(), t));
    }

    return resultPoints;
}

Point Bezier_loop(List<Point> points, List<Point> temp, double t) {
    for (int i = 1; i < points.Count; i++) {
        int deltaX = (int) (t * (points[i].X - points[i - 1].X) + 0.5),
            deltaY = (int) (t * (points[i].Y - points[i - 1].Y) + 0.5);

        temp.Add (new Point(points[i - 1].X + deltaX, points[i - 1].Y + deltaY));
    }
    if (temp.Count > 1) {
        return Bezier_loop (temp, new List<Point>(), t);
    } 
    else {
        return temp[0];
    }
}
```

