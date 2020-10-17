---
title: JavaScript从入门到入土
tags: [编程]
categories: 
- [JavaScript]
mathjax: true
date: 2020-09-09 00:00:00
---

## 数组

二维数组定义方法：

```js
var myarr = new Array();//先声明一维
for (let i = 0; i < 2; i++)//一维长度为2
{
    //再声明二维
    myarr[i] = new Array();
    for (let j = 0; j < 3; j++)//二维长度为3
    { 
        myarr[i][j] = i + j;// 赋值，每个数组元素的值为i+j
    }
}
```



## 事件

主要事件表：

|    事件     |         说明         |
| :---------: | :------------------: |
|   onclick   |     鼠标单击事件     |
| onmouseover |     鼠标经过事件     |
| onmouseout  |     鼠标移开事件     |
|  onchange   |  文本框内容改变事件  |
|  onselect   | 文本框内容被选中事件 |
|   onfocus   |       光标聚焦       |
|   onblur    |       光标离开       |
|   onload    |       网页导入       |
|  onunload   |       关闭网页       |



## 内置对象

### 日期对象

定义一个时间对象 :

```
var Udate=new Date(); 
```

**注意**：使用关键字new，Date()的首字母必须大写。 

使 `Udate` 成为日期对象，并且已有初始值：**当前时间(当前电脑系统时间)**。

如果要自定义初始值，可以用以下方法：

```js
var d = new Date(2012, 10, 1);  //2012年10月1日
var d = new Date('Oct 1, 2012'); //2012年10月1日
```

**访问方法语法：**“<日期对象>.<方法>”

常用方法：

|      方法名       |                  功能描述                   |
| :---------------: | :-----------------------------------------: |
|   get/setDate()   |                返回/设置日期                |
| get/setFullYear() |         返回/设置年份，用四位数表示         |
|   get/setYear()   |                返回/设置年份                |
|  get/setMonth()   | 返回/设置年份<br/>0:一月……11:十二月，所以+1 |
|  get/setHours()   |           返回/设置小时，24小时制           |
| get/setMinutes()  |               返回/设置分钟数               |
| get/setSeconds()  |               返回/设置秒钟数               |
|   get/setTime()   |         返回/设置时间（毫秒为单位）         |



### 字符串对象

|                方法名/语法                | 参数                                                         |                       功能描述                        |
| :---------------------------------------: | :----------------------------------------------------------- | :---------------------------------------------------: |
|           stringObject.length;            |                                                              |                  返回该字符串的长度                   |
|               toUpperCase()               |                                                              |              将字符串小写字母转换为大写               |
|               toLowerCase()               |                                                              |        将字符串所有大写字母都变成小写的字符串         |
|        stringObject.charAt(index)         | Index：必需。表示宇符串中某个位置的数字，即字符在字符串中的下标 |   返回指定位置的字符。返回的字符是长度为 1 的字符串   |
| stringObject.indexOf(substring, startpos) | substring：必需。规定需检索的字符串值<br/>startpos：可选的整数参数。规定在字符串中开始检索的位置。它的合法取值是0到 stringobject length-1●如省暗该参数，则将从字符串的首字符开始检索。 |    返回某个指定的字符串值在字符串中首次出现的位置     |
|    stringObject.split(separator,limit)    | separator：必需。从该参数指定的地方分割 stringObject<br/>limit：可选参数，分割的次数，如设置该参数，返回的子串不会多于这个参数指定的数组，如果无此参数为不限制次数 |        将字符串分割为字符串数组，并返回此数组         |
| stringObject.substring(startPos,stopPos)  | startPos：必需。一个非负的整数，开始位置。<br/>stopPos：可选。一个非负的整数，结束位置，如果省略该参数，那么返回的子串会一直到字符串对象的结尾。 |      用于提取字符串中介于两个指定下标之间的字符       |
|   stringObject.substr(startPos,length)    | startPos：必需。要提取的子串的起始位置。必须是数值。<br/>length：可选。提取字符串的长度。如果省略，返回从stringObject的开始位置startPos到 stringObject的结尾的字符 | 从字符串中提取从 startPos位置开始的指定数目的字符串。 |



### Math对象

Math 对象属性：

|  属性   |                      说明                      |
| :-----: | :--------------------------------------------: |
|    E    | 返回算术常里e，即自然对数的底数（约等于2.718） |
|   LN2   |         返回2的自然对数（约等于0.693）         |
|  Ln10   |        返回10的自然对数（约等于2.302）         |
|  LOG2E  |      返回以2为底的e的对数（约等于1.442）       |
| LOH10E  |      返回以10为底的e的对数（约等于0.434）      |
|   PI    |          返回圆周率（约等于3.14159）           |
| SQRT1_2 |       返回2的平方根的倒数（约等于0.707）       |
|  SQRT2  |          返回2的平方根（约等于1.414）          |

Math 对象方法：

|    方法    |                    描述                    |
| :--------: | :----------------------------------------: |
|   abs(x)   |               返回数的绝对值               |
|  acos(x)   |              返回数的反余弦值              |
|  asin(x)   |              返回数的反正弦值              |
|  atan(x)   |             返回数字的反正切值             |
| atan2(y,x) | 返回由x轴到点（x,y）的角度（以弧度为单位） |
|  ceil(x)   |               对数进行上舍入               |
|   cos(x)   |                返回数的余弦                |
|   exp(x)   |                返回e的指数                 |
|  floor(x)  |               对数进行下舍入               |
|   log(x)   |         返回数的自然对数（底为e）          |
|  max(x,y)  |             返回x和y中的最高值             |
|  min(x,y)  |             返回x和y中的最低值             |
|  pow(x,y)  |                返回x的y次幂                |
|  random()  |            返回0~1之间的随机数             |
|  round(x)  |         把数四舍五入为最接近的整数         |
|   sin(x)   |                返回数的正弦                |
|  sqrt(x)   |               返回数的平方根               |
|   tan(x)   |                返回角的正切                |
| toSource() |             返回该对象的源代码             |
| valueOf()  |            返回Math对象的原始值            |



### 数组对象

|      方法       |                            描述                            | 示例                                                         | 示例结果                              |
| :-------------: | :--------------------------------------------------------: | :----------------------------------------------------------- | :------------------------------------ |
|    concat()     |              连接两个或更多的数组，并返回结果              | var myarr1 = new Array(“1”);<br>var myarr2 = new Array(“2”,”3”);<br>document.write(myarr1.concat(myarr2)) | 1,2,3                                 |
|     join()      | 把数组的所有元素放入一个字符串元素通过指定的分隔符进行分隔 | var myarr1 = new Array(“1”);<br/>var myarr2 = new Array(“2”,”3”);<br/>var myarr3 = myarr1.concat(myarr2);<br/>document.write(myarr3.join(“-”)); | 1-2-3                                 |
|      pop()      |                刪除并返回数组的最后一个元素                |                                                              |                                       |
|     push()      |       向数组的末尾添加一个或更多元素，并返回新的长度       |                                                              |                                       |
|    reserve()    |                    颠倒数组中元素的顺序                    | var myarr1 = new Array(“我”,”爱”,”你”);<br/>document.write(myarr1.reverse()); | 你,爱,我                              |
|     shift()     |                  删除并返回组的第一个元素                  |                                                              |                                       |
|     slice()     |               从某个已有的数组返回选定的元素               | var myarr1 = new Array(“我”,”爱”,”你”);<br/>document.write(myarr1.slice(2)); | 爱,你                                 |
|     sort()      |                    对数组的元素进行排序                    | function sortNum(a,b) {  return a - b;  }<br/>//升序，如降序，把“a - b”该成“b - a” <br/>var myarr = new Array("80","16","50","6","100","1");<br/>document.write(myarr.sort()+“\\<br\\>”); <br/>document.write(myarr.sort(sortNum)); | 1,100,16,50,6,80<br/>1,6,16,50,80,100 |
|    splice()     |                删除元素，并向数组添加新元素                |                                                              |                                       |
|   toSource()    |                     返回该对象的源代码                     |                                                              |                                       |
|   toString()    |               把数组转换为字符串，并返回结果               |                                                              |                                       |
| toLocalString() |              把数组转换为本地数组，并返回结果              |                                                              |                                       |
|    unshift()    |       向数组的开头添加一个或更多元素，并返回新的长度       |                                                              |                                       |
|    valueOf()    |                    返回数组对象的原始值                    |                                                              |                                       |

### window对象

|      方法       |                      描述                      |
| :-------------: | :--------------------------------------------: |
|     alert()     |     显示带有一段消息和一个确认按钮的警告框     |
|    prompt()     |           显示可提示用户输入的对话框           |
|    confirm()    | 显示带有一段消息以及确认按钮和取消按钮的对话框 |
|     open()      |  打开一个新的浏览器窗口或查找一个已命名的窗口  |
|     close()     |                 关闭浏览器窗口                 |
|     print()     |               打印当前窗口的内容               |
|     focus()     |             把键盘焦点给予一个窗口             |
|     blur()      |            把键盘焦点从顶层窗口移开            |
|    moveBy()     |     可相对窗口的当前坐标把它移动指定的像素     |
|    moveTo()     |       把窗口的左上角移动到一个指定的坐标       |
|   resizeBy()    |          按照指定的像素调整窗口的大小          |
|   resizeTo()    |       把窗口的大小调整到指定的宽度和高度       |
|   scrollBy()    |           按照指定的像素值来滚动内容           |
|    sciolTo()    |             把内容滚动到指定的坐标             |
|  setInterval()  |             每隔指定的时间执行代码             |
|  setTimeout()   |         在指定的延迟时间之后来执行代码         |
| clearInterval() |            取消setInterval()的设置             |
| clearTimeout()  |             取消setTimeout()的设置             |

#### 计时器

|      方法       |             说明             |
| :-------------: | :--------------------------: |
|  setTimeout()   | 指定的延迟时间之后来执行代码 |
| clearTimeout()  |     取消setTimeout()设置     |
|  setInterval()  |    每隔指定的时间执行代码    |
| clearInterval() |    取消setInterval()设置     |

### history 对象

history对象记录了用户曾经浏览过的页面(URL)，并可以实现浏览器前进与后退相似导航的功能。

**注意:从窗口被打开的那一刻开始记录，每个浏览器窗口、每个标签页乃至每个框架，都有自己的history对象与特定的window对象关联。**

```js
window.history.[属性|方法]
```

|  属性  |             描述              |
| :----: | :---------------------------: |
| length | 返回浏览器历史列表中的URL数量 |

|    方法    |               描述                |
| :--------: | :-------------------------------: |
|   back()   |   加载history列表中的前-一个URL   |
| forward()  |   加载history列表中的下一个URL    |
| go(number) | 加载history列表中的某个具体的页面 |

### location对象

|   属性   |                  描述                   |
| :------: | :-------------------------------------: |
|   hash   |    设置或返回从井号(#)开始的URL (锚)    |
|   host   |    设置或返回主机名和当前URL的端口号    |
| hostname |        设置或返回当前URL的主机名        |
|   href   |           设置或返回完整的URL           |
| pathname |       设置或返回当前URL的路径部分       |
|   port   |        设置或返回当前URL的端口号        |
| protocol |         设置或返回当前URL的协议         |
|  search  | 设置或返回从问号(?)开始的URL (查询部分) |

|   方法    |          描述          |
| :-------: | :--------------------: |
| assign()  |      加载新的文档      |
| reload()  |    重新加载当前文档    |
| replace() | 用新的文档替换当前文档 |



### navigator对象

|    属性     |                    描述                    |
| :---------: | :----------------------------------------: |
| appCodeName |          浏览器代码名的字符串表示          |
|   appName   |              返回浏览器的名称              |
| appVersion  |         返回浏览器的平台和版本信息         |
|  platform   |        返回运行浏览器的操作系统平台        |
|  userAgent  | 返回由客户机发送服务器的user-agent头部的值 |

#### 几种浏览器的user_agent

| 浏览器  |                          userAgent                           |
| :-----: | :----------------------------------------------------------: |
| chrome  | Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, likeGecko) Chrome/34.0.1847.116 Safari/537.36p |
| firefox | Mozilla/5.0 (Windows NT 6.1; WOW64; rv:24.0) Gecko/20100101 Firefox/24.0 |
|  IE 8   | Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0;SLCC2; .NETCLR2.0.50727;.NET CLR3.5.30729;.NET CLR3.0.30729; .NET4.0C) |

### screen对象

screen对象用于获取用户的屏幕信息。

**语法：**

```js
window.screen.属性
```

|    属性     |                             描述                             |
| :---------: | :----------------------------------------------------------: |
| availHeight |               窗口可以使用的屏幕高度，单位像素               |
| availWidthe |               窗口可以使用的屏幕宽度，单位像素               |
| colorDepth  |      用户浏览器表示的颜色位数，通常为32位(每像素的位数)      |
| pixelDepth  | 用户浏览器表示的颜色位数，通常为32位(每像素的位数)(IE不支持此属性) |
|   height    |                     屏幕的高度，单位像素                     |
|    width    |                     屏幕的宽度，单位像素                     |





### DOM对象

#### 基础元素获取

|         方法         |             说明             | 获得 |
| :------------------: | :--------------------------: | :--: |
|    getElementByld    |      通过指定id获得元素      | 一个 |
|  getElementsByName   | 通过元素名称name属性获得元素 | 一组 |
| getElementsByTagName |     通过标签名称获得元素     | 一组 |

#### DOM节点

1. **元素节点：**上图中`<html>`、`<body>`、`<p>`等都是元素节点，即标签。

2. **文本节点：**向用户展示的内容，如`<li>...</li>`中的JavaScript、DOM、CSS等文本。
3. **属性节点：**元素属性，如`<a>`标签的链接属性`href="http://www.imooc.com"`。

**节点属性：**

|   方法    |                   说明                   |
| :-------: | :--------------------------------------: |
| innerHTML |               返回节点内容               |
| nodeName  |  返回一个字符串，其内容是给定节点的名字  |
| nodeType  | 返回一个整数，这个数值代表给定节点的类型 |
| nodeValue |           返回给定节点的当前值           |

<br/>

**遍历节点树:**

|      方法       |                      说明                       |
| :-------------: | :---------------------------------------------: |
|   childNodes    | 返回一个数组,这个数组由给定元素节点的子节点构成 |
|   firstChild    |                返回第一个子节点                 |
|    lastChild    |               返回最后一个子节点                |
|   parentNode    |            返回一个给定节点的父节点             |
|   nextSibling   |           返回给定节点的下一个子节点            |
| previousSibling |           返回给定节点的上一个子节点            |

**DOM操作:**

|          方法          |                          说明                          |
| :--------------------: | :----------------------------------------------------: |
| createElement(element) |                  创建一个新的元素节点                  |
|    createTextNode()    |          创建-一个包含着给定文本的新文本节点           |
|     appendChild()      |   指定节点的最后一个子节点列表之后添加一个新的子节点   |
|     insertBefore()     | 将一个给定节点插入到一个给定元素节点的给定子节点的前面 |
|     removeChild()      |             从一个给定元素中删除一个子节点             |
|     replaceChild()     |    把一个给定父元素里的一个子节点替换为另外一个节点    |



#### 网页卷曲距离与偏移量
{% asset_img Scroll-Offset.jpg Scroll-Offset %}