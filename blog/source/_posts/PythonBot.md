---
title: Python爬虫
tags: [编程]
categories: 
- [Python]
- [爬虫]
date: 2020-08-29
---

## Python基础知识

### 数据类型

- 整数
  - Python可以处理任意大小的整数，当然包括负整数，在Python程序中，整数的表示方法和数学上的写法一模一样，例如：`1`，`100`，`-8080`，`0`，等等。
  - 计算机由于使用二进制，所以，有时候用十六进制表示整数比较方便，十六进制用`0x`前缀和0-9，a-f表示，例如：`0xff00`，`0xa5b4c3d2`，等等。

- 浮点数
  - 浮点数也就是小数，之所以称为浮点数，是因为按照科学记数法表示时，一个浮点数的小数点位置是可变的，比如，1.23x10^9和12.3x10^8是相等的。浮点数可以用数学写法，如`1.23`，`3.14`，`-9.01`，等等。但是对于很大或很小的浮点数，就必须用科学计数法表示，把10用e替代，1.23x10^9就是**1.23e9**，或者**12.3e8**，0.000012可以写成**1.2e-5**，等等。

  - 整数和浮点数在计算机内部存储的方式是不同的，整数运算永远是精确的（除法难道也是精确的？是的！），而浮点数运算则可能会有四舍五入的误差。

- 字符串
  - 字符串是以`''`或`""`括起来的任意文本，比如**'abc'**，**"xyz"**等等。请注意，**''**或**""**本身只是一种表示方式，不是字符串的一部分，因此，字符串`'abc'`只有`a，b，c`这3个字符。

- 布尔值
  - 在Python中，可以直接用`True`、`False`表示布尔值（请注意大小写），也可以通过布尔运算计算出来。
  - 布尔值可以用`and`、`or`和`not`运算。

- 空值
  - 空值是Python里一个特殊的值，用`None`表示。None不能理解为0，因为0是有意义的，而None是一个特殊的空值。

### print语句

**print**语句可以向屏幕上输出指定的文字。

比如输出'hello, world'，用代码实现如下：

```python
print 'hello, world'
```

**注意：**

1.Python交互式环境下编写代码时，`>>>`是Python解释器的提示符，不是代码的一部分。

2.print语句也可以跟上多个字符串，用逗号“,”隔开，就可以连成一串输出：

```
print 'The quick brown fox', 'jumps over', 'the lazy dog'
The quick brown fox jumps over the lazy dog
```

print会依次打印每个字符串，遇到逗号“,”会输出一个空格，因此，输出的字符串是这样拼起来的：

### 注释

Python的注释以`#`开头，后面的文字直到行尾都算注释

```python
## 这一行全部都是注释...
print 'hello' ## 这也是注释
```

注释还有一个巧妙的用途，就是一些代码我们不想运行，但又不想删除，就可以用注释暂时屏蔽掉：

```python
## 暂时不想运行下面一行代码:
## print 'hello, python.'
```

### 字符串

字符串可以用`''`或者`""`括起来表示。

如果字符串本身包含`'`怎么办？比如我们要表示字符串` I'm OK `，这时，可以用`" "`括起来表示：

```
"I'm OK"
```

类似的，如果字符串包含`"`，我们就可以用`' '`括起来表示：

```
'Learn "Python"'
```

如果字符串既包含`'`又包含`"`怎么办？

这个时候，就需要对字符串的某些特殊字符进行“转义”，Python字符串用`\`进行转义。

要表示字符串 `Bob said "I'm OK".`
由于 ' 和 " 会引起歧义，因此，我们在它前面插入一个`\`表示这是一个普通字符，不代表字符串的起始，因此，这个字符串又可以表示为

```python
'Bob said \"I\'m OK\".'
```

**注意：**转义字符 \ 不计入字符串的内容中。

常用的转义字符还有：

```
\n 表示换行
\t 表示一个制表符
\\ 表示 \ 字符本身
```

### raw字符串与多行字符串

如果一个字符串包含很多需要转义的字符，对每一个字符都进行转义会很麻烦。为了避免这种情况，我们可以在字符串前面加个前缀` r `，表示这是一个 raw 字符串，里面的字符就不需要转义了。例如：

```python
r'\(~_~)/ \(~_~)/'
```

但是`r'...'`表示法不能表示多行字符串，也不能表示包含`'`和 `"`的字符串（为什么？）

如果要表示多行字符串，可以用`'''...'''`表示：

```python
'''Line 1
Line 2
Line 3'''
```

上面这个字符串的表示方法和下面的是完全一样的：

'Line 1\nLine 2\nLine 3'

还可以在多行字符串前面添加` r `，把这个多行字符串也变成一个raw字符串：

```python
r'''Python is created by "Guido".
It is free and easy to learn.'''
```

### 整数和浮点数

Python支持对整数和浮点数直接进行四则混合运算，运算规则和数学上的四则运算规则完全一致。

基本的运算：

```python
1 + 2 + 3   ## ==> 6
4 * 5 - 6   ## ==> 14
7.5 / 8 + 2.1   ## ==> 3.0375
```

使用括号可以提升优先级，这和数学运算完全一致，注意只能使用小括号，但是括号可以嵌套很多层：

```python
(1 + 2) * 3    ## ==> 9
(2.2 + 3.3) / (1.5 * (9 - 0.3))    ## ==> 0.42145593869731807
```

和数学运算不同的地方是，Python的整数运算结果仍然是整数，浮点数运算结果仍然是浮点数：

```python
1 + 2    ## ==> 整数 3
1.0 + 2.0    ## ==> 浮点数 3.0
```

但是整数和浮点数混合运算的结果就变成浮点数了：

```
1 + 2.0    ## ==> 浮点数 3.0
```

为什么要区分整数运算和浮点数运算呢？这是因为整数运算的结果永远是精确的，而浮点数运算的结果不一定精确，因为计算机内存再大，也无法精确表示出无限循环小数，比如` 0.1 `换成二进制表示就是无限循环小数。

那整数的除法运算遇到除不尽的时候，结果难道不是浮点数吗？我们来试一下：

```python
11 / 4    ## ==> 2
```

令很多初学者惊讶的是，Python的整数除法，即使除不尽，结果仍然是整数，余数直接被扔掉。不过，Python提供了一个求余的运算 % 可以计算余数：

```python
11 % 4    ## ==> 3
```

如果我们要计算 11 / 4 的精确结果，按照“整数和浮点数混合运算的结果是浮点数”的法则，把两个数中的一个变成浮点数再运算就没问题了：

```python
11.0 / 4    ## ==> 2.75
```



### 布尔类型

**与运算**：只有两个布尔值都为 True 时，计算结果才为 True。

```python
True and True   ## ==> True
True and False   ## ==> False
False and True   ## ==> False
False and False   ## ==> False
```

**或运算**：只要有一个布尔值为 True，计算结果就是 True。

```python
True or True   ## ==> True
True or False   ## ==> True
False or True   ## ==> True
False or False   ## ==> False
```

**非运算**：把True变为False，或者把False变为True：

```python
not True   ## ==> False
not False   ## ==> True
```

布尔运算在计算机中用来做条件判断，根据计算结果为True或者False，计算机可以自动执行不同的后续代码。

在Python中，布尔类型还可以与其他数据类型做 and、or和not运算，请看下面的代码：

```python
a = True
print a and 'a=T' or 'a=F'
```

计算结果不是布尔类型，而是字符串 'a=T'，这是为什么呢？

因为Python把`0`、`空字符串''`和`None`看成 False，其他数值和非空字符串都看成 True，所以：

```python
True and 'a=T' 计算结果是 'a=T'
继续计算 'a=T' or 'a=F' 计算结果还是 'a=T'
```

**要解释上述结果，又涉及到 and 和 or 运算的一条重要法则：短路计算。**

1. 在计算` a and b `时，如果 a 是 False，则根据与运算法则，整个结果必定为 False，因此返回 a；如果 a 是 True，则整个计算结果必定取决与 b，因此返回 b。

2. 在计算` a or b `时，如果 a 是 True，则根据或运算法则，整个计算结果必定为 True，因此返回 a；如果 a 是 False，则整个计算结果必定取决于 b，因此返回 b。

所以Python解释器在做布尔运算时，只要能提前确定计算结果，它就不会往后算了，直接返回结果。

### list

#### 创建list

Python内置的一种数据类型是列表：`list`。list是一种有序的集合，可以随时添加和删除其中的元素。

比如，列出班里所有同学的名字，就可以用一个list表示：

```python
['Michael', 'Bob', 'Tracy']
['Michael', 'Bob', 'Tracy']
```

list是数学意义上的有序集合，也就是说，list中的元素是按照顺序排列的。

构造list非常简单，按照上面的代码，直接用` [ ] `把list的所有元素都括起来，就是一个list对象。通常，我们会把list赋值给一个变量，这样，就可以通过变量来引用list：

```python
classmates = ['Michael', 'Bob', 'Tracy']
classmates ## 打印classmates变量的内容
['Michael', 'Bob', 'Tracy']
```

由于Python是动态语言，所以list中包含的元素并不要求都必须是同一种数据类型，我们完全可以在list中包含各种数据：

```python
L = ['Michael', 100, True]
```

一个元素也没有的list，就是空list：

```python
empty_list = []
```

#### 按照索引访问list

由于list是一个有序集合，所以，我们可以用一个list按分数从高到低表示出班里的3个同学：

```python
L = ['Adam', 'Lisa', 'Bart']
```

那我们如何从list中获取指定第 N 名的同学呢？方法是通过索引来获取list中的指定元素。

**需要特别注意的是**，索引从 0 开始，也就是说，第一个元素的索引是0，第二个元素的索引是1，以此类推。

因此，要打印第一名同学的名字，用 L[0]:

```python
print L[0]
Adam
```

要打印第二名同学的名字，用 L[1]:

```python
print L[1]
Lisa
```

要打印第三名同学的名字，用 L[2]:

```python
print L[2]
Bart
```

要打印第四名同学的名字，用 L[3]:

```python
print L[3]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
```

报错了！IndexError意思就是索引超出了范围，因为上面的list只有3个元素，有效的索引是 0，1，2。

所以，使用索引时，**千万注意不要越界**。



#### 倒序访问list

我们还是用一个list按分数从高到低表示出班里的3个同学：

```python
L = ['Adam', 'Lisa', 'Bart']
```

这时，老师说，请分数最低的同学站出来。

要写代码完成这个任务，我们可以先数一数这个 list，发现它包含3个元素，因此，最后一个元素的索引是2：

```python
print L[2]
Bart
```

有没有更简单的方法？有！

Bart同学是最后一名，俗称倒数第一，所以，我们可以用 -1 这个索引来表示最后一个元素：

```python
print L[-1]
Bart
```

Bart同学表示躺枪。

类似的，倒数第二用 -2 表示，倒数第三用 -3 表示，倒数第四用 -4 表示：

```python
print L[-2]
Lisa
print L[-3]
Adam
print L[-4]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
```

L[-4] 报错了，因为倒数第四不存在，一共只有3个元素。

使用倒序索引时，也要注意**不要越界**。

#### 给list添加新元素

现在，班里有3名同学：

```python
L = ['Adam', 'Lisa', 'Bart']
```

今天，班里转来一名新同学 Paul，如何把新同学添加到现有的 list 中呢？

第一个办法是用 list 的` append() `方法，把新同学追加到 list 的末尾：

```python
L = ['Adam', 'Lisa', 'Bart']
L.append('Paul')
print L
['Adam', 'Lisa', 'Bart', 'Paul']
```

**append()**总是把新的元素添加到 list 的尾部。

如果 Paul 同学表示自己总是考满分，要求添加到第一的位置，怎么办？

方法是用list的` insert()`方法，它接受两个参数，第一个参数是索引号，第二个参数是待添加的新元素：

```python
L = ['Adam', 'Lisa', 'Bart']
L.insert(0, 'Paul')
print L
['Paul', 'Adam', 'Lisa', 'Bart']
```

**L.insert(0, 'Paul')** 的意思是，'Paul'将被添加到索引为 0 的位置上（也就是第一个），而原来索引为 0 的Adam同学，以及后面的所有同学，都自动向后移动一位。

#### 从list删除元素

Paul同学刚来几天又要转走了，那么我们怎么把Paul 从现有的list中删除呢？

如果Paul同学排在最后一个，我们可以用list的`pop()`方法删除：

```python
L = ['Adam', 'Lisa', 'Bart', 'Paul']
L.pop()
'Paul'
print L
['Adam', 'Lisa', 'Bart']
```

**pop()**方法总是删掉list的最后一个元素，并且它还返回这个元素，所以我们执行 L.pop() 后，会打印出 'Paul'。

如果Paul同学不是排在最后一个怎么办？比如Paul同学排在第三：

```python
L = ['Adam', 'Lisa', 'Paul', 'Bart']
```

要把Paul踢出list，我们就必须先定位Paul的位置。由于Paul的索引是2，因此，用` pop(2)`把Paul删掉：

```python
L.pop(2)
'Paul'
print L
['Adam', 'Lisa', 'Bart']
```

#### 替换元素

假设现在班里仍然是3名同学：

```python
L = ['Adam', 'Lisa', 'Bart']
```

现在，Bart同学要转学走了，碰巧来了一个Paul同学，要更新班级成员名单，我们可以先把Bart删掉，再把Paul添加进来。

另一个办法是直接用Paul把Bart给替换掉：

```python
L[2] = 'Paul'
print L
L = ['Adam', 'Lisa', 'Paul']
```

对list中的某一个索引赋值，就可以直接用新的元素替换掉原来的元素，list包含的元素个数保持不变。

由于Bart还可以用 -1 做索引，因此，下面的代码也可以完成同样的替换工作：

```python
L[-1] = 'Paul'
```

#### 创建tuple

tuple是另一种有序的列表，中文翻译为“ 元组 ”。tuple 和 list 非常类似，但是，tuple一旦创建完毕，就不能修改了。

同样是表示班里同学的名称，用tuple表示如下：

```python
t = ('Adam', 'Lisa', 'Bart')
```

创建tuple和创建list唯一不同之处是用`( )`替代了`[ ]`。

现在，这个` t `就不能改变了，tuple没有 append()方法，也没有insert()和pop()方法。所以，新同学没法直接往 tuple 中添加，老同学想退出 tuple 也不行。

获取 tuple 元素的方式和 list 是一模一样的，我们可以正常使用 t[0]，t[-1]等索引方式访问元素，但是不能赋值成别的元素，不信可以试试：

```python
t[0] = 'Paul'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

#### 单元素tuple

tuple和list一样，可以包含 0 个、1个和任意多个元素。

包含多个元素的 tuple，前面我们已经创建过了。

包含 0 个元素的 tuple，也就是空tuple，直接用 ()表示：

```python
t = ()
print t
()
```

创建包含1个元素的 tuple 呢？来试试：

```python
t = (1)
print t
1
```

好像哪里不对！t 不是 tuple ，而是整数1。**为什么**呢？

因为`()`既可以表示tuple，又可以作为括号表示运算时的优先级，结果 (1) 被Python解释器计算出结果 1，导致我们得到的不是tuple，而是整数 1。

正是因为用()定义单元素的tuple有歧义，所以 Python 规定，单元素 tuple 要多加一个逗号“,”，这样就避免了歧义：

```python
t = (1,)
print t
(1,)
```

Python在打印单元素tuple时，也自动添加了一个“,”，为了更明确地告诉你这是一个tuple。

多元素 tuple 加不加这个额外的“,”效果是一样的：

```python
t = (1, 2, 3,)
print t
(1, 2, 3)
```

#### “可变”的tuple

前面我们看到了tuple一旦创建就不能修改。现在，我们来看一个“可变”的tuple：

```python
t = ('a', 'b', ['A', 'B'])
```

**注意**到 t 有 3 个元素：**'a'，'b'**和一个list：**['A', 'B']**。list作为一个整体是tuple的第3个元素。list对象可以通过 t[2] 拿到：

```python
L = t[2]
```

然后，我们把list的两个元素改一改：

```python
L[0] = 'X'
L[1] = 'Y'
```

再看看tuple的内容：

```python
print t
('a', 'b', ['X', 'Y'])
```

表面上看，tuple的元素确实变了，但其实变的不是 tuple 的元素，而是list的元素。

tuple一开始指向的list并没有改成别的list，所以，tuple所谓的**“不变”**是说，tuple的每个元素，指向永远不变。即**指向'a'，就不能改成指向'b'**，指向一个list，就不能改成指向其他对象，但指向的这个list本身是可变的！

理解了**“指向不变”**后，要创建一个内容也不变的tuple怎么做？那就必须保证tuple的每一个元素本身也不能变。





## 爬虫简介

### 爬虫是什么

> 通过编写这个程序，使其模拟进行浏览器上网，进而在互联网上抓取数据的过程

### 爬虫的价值

> 抓取互联网上的数据，为人所用，有了大量的数据，就如同有了一个数据银行一样
>
> 下一步做的就是如何将这些爬取的数据产品化、商业化

------

- ### 爬虫可能带来的风险

  - 爬虫干扰了被访问网站的正常运营
  - 爬虫抓取了收到法律保护的特定类型的数据或信息

- ### 如何在使用/编写爬虫的过程中，避免进入"局子"的厄运

  - 时常的优化自己的程序，避免干扰被访问网站的正常运行
  - 在使用，传播爬取到的数据时，审查抓取到的内容
  - 如果发现了涉及到用户商业机密等敏感内容,需要及时停止爬取或传播


- ### 爬虫在使用场景中的分类

  - 通用爬虫：抓取系统重要组成部分。抓取的是一整张页面数据
  - 聚焦爬虫：是建立在通用爬虫的基础之上。抓取的是页面中特定的局部内容
  - 增量式爬虫：检测网站中数据更新的情况。只会抓取网站中最新更新出来的数据


- ### 爬虫的矛与盾

  - 反爬机制：门户网站可以通过制定相应的策略或者技术手段，防止爬虫程序进行网站数据的爬取

  - 反反爬策略：爬虫程序可以通过制定相关的策略或者技术手段，破解门户网站中具备的反爬机制，从而可以获取门户网站中相关的数据

  - robots.txt协议："君子协议"，规定了网站中哪些数据可以被爬取，哪些数据不可以被爬取

  - 如何查看robots.txt协议：网站域名 + '/robots.txt'

> 例如：淘宝的域名为`www.taobao.com`，则协议地址为`www.taobao.com/robots.txt`



## http&https协议

### http协议

#### 概念：服务器和客户端进行数据交互的一种形式。

- 常用请求头信息
  - `User-Agent`：请求载体的身份标识
  - `Connection`：请求完毕后，是断开连接还是保持连接
- 常用响应头信息
  - `Content-Type`：服务器响应回客户端的数据类型

### https协议

#### 概念：安全的超文本传输协议

- 加密方式
  - 对称秘钥加密
  - 非对称秘钥加密
  - 证书秘钥加密



## requests 模块

### 概念

> python中原生的一款基于网络请求的模块，功能非常强大，简单便捷，效率极高。
>
> 作用：模拟浏览器发送请求。
>
> 爬取页面中指定的页面内容，属于**聚焦爬虫**。

### 开始使用

- 环境安装（需要先安装python编译环境）

```shell
pip install requests
```

- 使用流程/编码流程
  - 指定URL
  - 基于requests模块发起请求
  - 获取响应对象中的数据值
  - 持久化存储

### 第一个爬虫程序

需求：爬取搜狗首页的页面数据

``` python
## 导包
import requests

## step1：指定url
url = 'https://www.sogou.com/'

## step_2：发起请求：使用get方法发起get请求，该方法会返回一个响应对象。参数url表示请求对应的url
response = requests.get(url=url)

## step_3：获取响应数据：通过调用响应对象的text属性，返回响应对象中存储的字符串形式的响应数据（页面源码数据）
page_text = response.text

## step_4：持久化存储
with open('./sogou.html', 'w', encoding='utf-8') as fp:
    fp.write(page_text)
print("爬取数据完毕")
```

### 实战案例

#### 1. 爬取搜狗指定词条对应的搜索结果页面（简易网页采集器）

> [!warning|style:callout|label:案例引入|labelVisibility:visible|iconVisibility:visible]
> `UA:User-Agent`（请求载体的身份标识）
>
> UA检测：门户网站的服务器会监测对应请求的载体身份标识。
>
> 如果检测到 请求的载体身份标识 为某一款浏览器，则说明该请求为正常请求；
>
> 如果检测到 请求的载体身份标识 不是浏览器，则表示为不正常请求（爬虫），
>
> 则服务端有可能拒绝该请求，导致爬取失败。

##### 代码实现 

```python
import requests
## 1.指定url
url = 'https://www.sogou.com/web'

## 2.UA伪装：将对应的User-Agent封装到字典中
headers = {
    ## 标识字符串可通过浏览器的开发者选项Network中获取
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36'
}

## 3.处理url携带的参数：封装到字典中
kw = input('输入关键词/Enter a word：')
data = {
    'query': kw
}

## 4.对指定的url发起的请求，对应的url携带参数，并在请求过程中处理参数
response = requests.get(url=url, params=data, headers=headers)
page_text = response.text

## 6.持久化存储
fileName = kw + '.html'
with open(fileName, 'w', encoding='utf-8') as fp:
    fp.write(page_text)
print(fileName, '保存成功')
```

<br/>

#### 2. 破解百度翻译

> [!tip|style:callout|label:思路|labelVisibility:visible|iconVisibility:visible]
>
> 若此操作**引起页面局部刷新，网页地址不变**，则可以判断此操作发出的是**阿贾克斯（`XHR`）请求**。
>
> 通过抓包工具，获取阿贾克斯（`XHR`）请求，找到带有被翻译字段参数的Post请求链接。
>
> 获取的响应数据（Content-Type）是一组`json`数据。

##### 代码实现 

```python
import requests
import json  ## 引入json库
## 1.指定url
post_url = 'https://fanyi.baidu.com/sug'

## 2.UA伪装
header = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36'
}

## 3.post请求参数处理（同get请求一致）
word = input('Enter a word:')
data = {
    'kw': word
}

## 4.请求发送
response = requests.post(url=post_url, data=data, headers=header)

## 5.获取响应数据：json()方法返回obj（需确认服务器响应类型为json）
dic_obj = response.json()

## 6.持久化存储
fileName = word+'.json'
fp = open(fileName, 'w', encoding='utf-8')
json.dump(dic_obj, fp=fp, ensure_ascii=False)

print('操作完成!')
```

 <br/>

#### 3. 爬取豆瓣电影分类排行榜`https://movie.douban.com`中的电影详情数据

> [!tip|style:callout|label:思路|labelVisibility:visible|iconVisibility:visible]
> 在豆瓣电影官网点击"排行榜"，进入"喜剧"分类
>
> 所需要获取的，就是这个局部页面内的电影名称等基本信息
>
> 当页面到达最底部时，发现页面获取了新的请求反馈，加载出更多电影信息，且页面地址未变化，因此判断页面发出了阿贾克斯（`XHR`）请求
>
> 在抓包工具中的`Network - XHR`下捕获对应的请求信息，确认`Content-Type`属性为`application/json`后，**获取其`url`，请求类型（GET），以及所有来自表单信息（`Form Data`）的键值对**
>
> 最后分析键值对内的几个关键参数，并自定义使用

##### 代码实现 

```python
import requests
import json
## 1.指定url
url = 'https://movie.douban.com/j/chart/top_list'

## 2.UA伪装
header = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36'
}

## 3.请求参数处理
data = {
    'type': '24',
    'interval_id': '100:90',
    'action': '',
    'start': '0',  ## 表示从第几部电影开始获取
    'limit': '20'  ## 一次获取的信息个数
}

## 4.请求发送
response = requests.get(url=url, params=data, headers=header)

## 5.获取列表数据：json()方法返回obj
list_data = response.json()

## 6.持久化存储
fp = open('./movies.json', 'w', encoding='utf-8')
json.dump(list_data, fp=fp, ensure_ascii=False)

print('获取结束!')
```

<br/>

#### 4. 爬取肯德基餐厅查询`http://www.kfc.com.cn/kfccda/index.aspx`中指定地点的餐厅数

> 根据`Content-Type: text/plain; charset=utf-8`这一属性，返回的结果为text，因此无需调用`json`
>
> 同样指定`url`为请求包内的链接，获取`Form Data`内的参数作为请求的参数

```python
import requests
## 1.指定url
url = 'http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=keyword'

## 2.UA伪装
header = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36'
}

## 3.请求参数处理
kw = input('输入关键字：')
data = {
    'cname': '',
    'pid': '',
    'keyword': kw,
    'pageIndex': '1',  ## 指定页码
    'pageSize': '10'  ## 每页显示的实例个数
}

## 4.发送post请求
response = requests.post(url=url, data=data, headers=header)

## 5.获取列表数据
list_data = response.text

## 6.持久化存储
fileName = kw + '.txt'
with open(fileName, 'w', encoding='utf-8') as fp:
    fp.write(list_data)

print('获取结束!')
```

<br/>

#### 5. 【进阶】爬取国家药品监督管理总局中 基于中华人民共和国化妆品生产许可证相关数据`http://125.35.6.84:81/xk`

#### 案例分析

> [!note|style:callout|label:思路|labelVisibility:visible|iconVisibility:visible]
> 此链接的可见内容里并不包含许可证的详细信息，无法确定这个链接是否包含详细信息
>
> 但是可以确定的是，每一个企业名称都对应一个超链接，此链接内包含对应企业的详细数据。
>
> 因此可以尝试，从请求链接中获取页面的标签数据，通过查找`<a>`标签中的`herf`属性获取详细数据的链接

第一步：初步代码，确认对网址请求是否能获取对应页面

```python
import requests
## 指定url
url = 'http://125.35.6.84:81/xk'

## UA伪装
header = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36'
}

#发送get请求 + 取列表数据
page_text = requests.get(url=url, headers=header).text

## 持久化存储
with open('./HuaZhuangPin.html', 'w', encoding='utf-8') as fp:
    fp.write(page_text)

print('获取结束!')
```

> 验证发现，这种请求方式并不能获取页面内的详细信息，因此不能通过对`url`请求达成预期结果。
>
> 则可以判断，对原`url`请求的内容可能没有企业信息。很有可能时动态加载的信息，需要通过Ajax（`XHR`）请求来获取信息

第二步：刷新页面，确认是否产生`XHR`请求数据

> 验证发现，`XHR`得到了一组`json`数据，包含了页面内列表内的数据
>
> 但我们想的到的是列表内`url`对应的详细数据，于是进一步分析数据内部，发现并没有与`url`关联的数据，但是存在一个`ID`属性
>
> - 通过对详情页`url`的观察发现
>   - `url`的域名都是一样的，只有携带的参数(ID)不一样
>   - ID值可以从首页对应的Ajax请求到的`json`串中获取
>   - 域名和ID值能够拼接出一个完整的企业对应的详情页的`url`

但是这里还有一个坑，还需要确定新得到的详情页数据是否是**来自动态加载的请求**，若是来自动态加载的请求，获取页面数据也就没有意义。

因此我们可以选择用第一步的方法尝试获取，但这样的方法效率极低，可以改成改用抓包工具来查看页面的内容。抓包查看`All`下的数据包，发现没有企业的数据。

所以得出结论，详情页的页面数据也是动态加载出来的！

第三步：刷新详情页面，确认是否产生`XHR`请求数据

> 验证发现，页面的确产生了数据类型为`json`的`HRX`请求，且请求内携带的参数为`id`

------

> 至此就可以得到**完整解决方案**：
>
> - 通过对原链接发起Ajax请求，获取`json`列表内的`ID`属性
> - 将请求内的`url`与获取到的`ID`拼接，将其作为新的`url`
> - 对新的`url`发起Ajax请求，获取最终的详细数据
> - 优化思路后的**解决方案**：批量获取页面内的`ID`参数，分别与请求的`url`结合，获取对应的企业详细数据

#### 代码实现

```python
import requests
import json
## UA伪装
header = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36'
}

## 获取首页加载请求的数据
url = 'http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsList'
## 对应参数的封装
data = {
    'on': 'true',
    'page': '1',  ## 获取的页码
    'pageSize': '15',  ## 每页显示的数量
    'productName': '',
    'conditionType': '1',
    'applyname': '',
    'applysn': ''
}

## 发送请求，并获取请求响应数据
json_ids = requests.post(url=url, data=data, headers=header).json()

## 遍历获取的json字典中ID数据所在的列表，批量获取并存储id
id_list = []  ## 用于存储企业id的数组
for dic in json_ids['list']:
    id_list.append(dic['ID'])  ## 将ID值存入存储列表中

## 打印查看是否正确获取
## print(id_list)

## --------------------

## 获取企业详情数据
post_url = 'http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsById'
## 遍历id列表，分别与post_url结合发起请求
all_data_list = []  ## 存储所有企业的详细数据
for id in id_list:
    ## 对应参数的封装
    data = {
        'id': id
    }
    ## 发送请求，并获取请求响应数据
    detail_json = requests.post(url=post_url, data=data, headers=header).json()

    ## 打印查看是否正确获取
    ## print(detail_json,'\n----------end----------')
    all_data_list.append(detail_json)  ## 将企业信息存入存储列表中

## 持久化存储
fp = open('./allData.json', 'w', encoding='utf-8')
json.dump(all_data_list, fp=fp, ensure_ascii=False)

print('Over!')
```

#### 代码优化

上述代码只能获取到其中一页的详细信息，若想要一次性多页（或所有）信息，只需要在页码的绑定数据外添加一个循环，将循环数字转为字符串后动态赋值给绑定数据，最后把只需执行一次的定义类代码移动到前面即可

```python
import requests
import json
## UA伪装
header = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36'
}

id_list = []  ## 用于存储企业id的数组
all_data_list = []  ## 存储所有企业的详细数据

## 获取首页加载请求的数据
url = 'http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsList'

## 添加循环，假设只获取前5页
for page in range(1, 6):
    page = str(page)  ## 将数字转为字符串
    ## 对应参数的封装
    data = {
        'on': 'true',
        'page': page,  ## 获取的页码
        'pageSize': '15',  ## 每页显示的数量
        'productName': '',
        'conditionType': '1',
        'applyname': '',
        'applysn': ''
    }
    ## 发送请求，并获取请求响应数据
    json_ids = requests.post(url=url, data=data, headers=header).json()
    ## 遍历获取的json字典中ID数据所在的列表，批量获取并存储id
    for dic in json_ids['list']:
        id_list.append(dic['ID'])  ## 将ID值存入存储列表中

## --------------------

## 获取企业详情数据
post_url = 'http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsById'
## 遍历id列表，分别与post_url结合发起请求

for id in id_list:
    ## 对应参数的封装
    data = {
        'id': id
    }
    ## 发送请求，并获取请求响应数据
    detail_json = requests.post(url=post_url, data=data, headers=header).json()
    ## 将企业信息存入存储列表中
    all_data_list.append(detail_json)

## 持久化存储
fp = open('./allData.json', 'w', encoding='utf-8')
json.dump(all_data_list, fp=fp, ensure_ascii=False)

print('Over!')
```

## 数据解析

- 聚焦爬虫：爬取页面中指定的页面内容
- 编码流程
  - 指定URL
  - 发起请求
  - 获取相应数据
  - $\color{red}数据解析$
  - 持久化存储

### 数据解析的方法/分类

- 正则
- bs4
- xpath(语言通用性强)

### 数据解析的原理概述

解析的局部文本内容都会在**标签之间**或**标签对应的属性中**进行存储

因此可以这么做：

1. 进行指定标签的定位

2. 标签或者标签对应的属性中存储的数据值进行提取（解析）

### 正则表达式方法

#### 常用的正则表达式

|          符号          |                             含义                             |              举例              |
| :--------------------: | :----------------------------------------------------------: | :----------------------------: |
|      **单字符：**      |                                                              |                                |
|           .            |                    除了换行以外的所有字符                    |                                |
|           []           |                   匹配集合中的任意一个字符                   |          [aoe];[a-w]           |
|           \d           |                             数字                             |             [0-9]              |
|           \D           |                            非数字                            |                                |
|           \w           |                   数字、字母、下划线、中文                   |                                |
|           \W           |                             非\w                             |                                |
|           \s           | 所有的空白字符包，包括空格、制表符、换页符等<br/>等价于【\f\n\r\t\v】 |                                |
|           \S           |                            非空白                            |                                |
|     **数量修饰：**     |                                                              |                                |
|           *            |                         任意多次 >=0                         |                                |
|           +            |                         至少一次 >=1                         |                                |
|           ?            |                      可有可无 0次或1次                       |                                |
|          {m}           |                           固定m次                            |                                |
|         {m,n}          |                            m-n次                             |                                |
|       **边界：**       |                                                              |                                |
|           ^            |                         以某符号开头                         |             ^begin             |
|           $            |                         以某符号结尾                         |              end$              |
|       **分组：**       |                                                              |                                |
|          (ab)          |                         a和b字符成组                         |                                |
|     **贪婪模式：**     |                                                              |                                |
|           .*           |                                                              |                                |
| **非贪婪(惰性)模式：** |                                                              |                                |
|          .*?           |                                                              |                                |
|                        |                                                              |                                |
|          re.I          |                          忽略大小写                          |                                |
|          re.M          |                           多行匹配                           |                                |
|          re.S          |                           单行匹配                           |                                |
|         re.sub         |                    在指定内容替换指定字符                    | re.sub(正则, 替换内容, 字符串) |
|                        |                                                              |                                |

#### 正则练习

```python
import re
## 提取python
re.findall('python', key)[0]

## 提取hello world
key = "<html><h1>hello world</h1></html>"
re.findall('<h1>(.*)</h1>', key)[0]

## 提取170
string = '我喜欢身高为170的女孩'
re.findall('\d+', string)

## 提取http://和https://
key = 'lalala<hTml>hello</HtMl>hahaha'  ## 输出<hTml>hello</HtMl>
re.findall('<[Hh][Tt][mM][lL]>(.*)</[Hh][Tt][mM][lL]>', key)

## 提取hit.
key = 'bobo@hut.edu.com'
re.findall('h.*?\.', key)

## 匹配sas和saas
key = 'saas and sas and saaas'
re.findall('sa{1,2}s', key)
```

#### 实例

项目需求：爬取糗事百科指定页面的糗图，并将其保存到指定文件夹中

```python
import requests
## 爬取图片数据
url = 'https://pic.qiushibaike.com/system/pictures/12172/121721055/medium/9OSVY4ZSU4NN6T7V.jpg'
## text(字符串数据)、content(二进制数据)、json()
img_data = requests.get(url=url).content

## 持久化存储
with open('./qiutu.jpg', 'wb') as fp:
    fp.write(img_data)
```

