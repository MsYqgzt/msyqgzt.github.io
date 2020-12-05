---
title: jQuery 样式篇
tags: [编程,前端,Web框架]
categories: 
- [JavaScript]
- [Node]
mathjax: true
date: 2020-11-22
---



## 初探jQuery

> jQuery 分 2 个系列版本 1.x 与 2.x，主要的区别在于**2.x 不再兼容 IE6、7、8浏览器**，这样做的目的是为了**兼容移动端开发**。由于减少了一些代码，使得该版本比 jQuery 1.x 更小、更快。
>
> 如果开发者比较在意老版本 IE 用户，只能使用 jQuery 1.9 及之前的版本了。这里为了兼容性问题，使用的是 1.9 以上版本。
>
> jQuery 每一个系列版本分为：压缩版(compressed) 与 开发版(development)
>
> - 我们**在开发过程中使用开发版**（开发版本便于代码修改及调试）
> - 项目**上线发布使用压缩版**（因为压缩版本体积更小，效率更快）。

 jQuery是一个JavaScript脚本库，不需要特别的安装，只需要我们在页面`<head> `标签内中，通过`script`标签引入` jQuery`库即可。

```html
<!DOCTYPE HTML>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
    <title>环境搭建</title>
</head> 
<body>
    <script type="text/javascript"> alert($) </script>
</body>
</html>
```

> **为什么没有在 `<script>` 标签中使用`type="text/javascript"` ？**
>
> 在 HTML5 中，不必那样做了。JavaScript 是 HTML5 以及所有现代浏览器中的默认脚本语言

### 老规矩 - HelloWorld

最简单的案例，**当页面加载完成后**，在页面中以居中的方式显示“Hello World”，效果如下：

<div style="padding:8px 0px;font-size:12px;text-align:center;border:1px solid #888;">Hello World</div>

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"/>
    <title>第一个简单的jQuery程序</title>
    <style type="text/css">
        div{
            padding:8px 0px;
            font-size:12px;
            text-align:center;
            border:1px solid #888;
        }
    </style>
    
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
    <script type="text/javascript">
        $(document).ready(function() {
            $("div").html("Hello World");
        });
    </script>
</head>
    
<body>
    <div></div>
</body>
</html>
```

> `$(document).ready`的作用是等页面的文档（document）中的**节点都加载完毕后，**再执行后续的代码，因为我们在执行代码的时候，可能会依赖页面的某一个元素，我们要确保这个元素真正的的被加载完毕后才能正确的使用。

### jQuery对象与DOM对象的区别

> 这里先做个大概印象，后面再深入分析

我们需要清楚认识一点：**jQuery对象与DOM对象是不一样的**

举个🌰，我们要获取页面上这个id为test的p元素，然后给这个文本节点增加一段文字：“你好”，并且让文字颜色变成红色。

1. 普通处理方式，通过标准JavaScript处理

```js
var p = document.getElementById('test');
p.innerHTML = '你好';
p.style.color = 'red';
```

通过原生DOM模型提供的`document.getElementById(“test”)`方法获取的DOM元素就是一个DOM对象，再通过`innerHTML`与`style`属性处理文本与颜色。



2. jQuery的处理

```js
var $p = $('#test');
$p.html('你好').css('color','red');
```

通过`$('#test')`方法会得到一个`$p`的jQuery对象，$p是一个类数组对象。这个对象里面包含了DOM对象的信息，然后封装了很多操作方法，调用自己的方法html与css，得到的效果与标准的JavaScript处理结果是一致的。

### jQuery对象与DOM对象的转换

jQuery库本质上还是JavaScript代码，它只是对JavaScript语言进行包装处理。我们使用jQuery的同时也能混合JavaScript原生代码一起使用。

在很多场景中，**我们需要jQuery与DOM能够相互的转换**，它们都是可以操作的DOM元素，jQuery是一个类数组对象，而DOM对象就是一个单独的DOM元素。

#### jQuery转DOM

1. **利用数组下标的方式读取到jQuery中的DOM对象**

HTML代码

```html
<div>元素一</div>
<div>元素二</div>
<div>元素三</div>
```

JavaScript代码

```js
var $div = $('div'); //jQuery对象
var div = $div[0]; //转化成DOM对象
div.style.color = 'red'; //操作dom对象的属性
```

2. **通过jQuery自带的get()方法**

jQuery对象自身提供一个`.get()`方法允许我们直接访问jQuery对象中相关的DOM节点，get方法中提供一个元素的索引：

```js
var $div = $('div'); //jQuery对象
var div = $div.get(0); //通过get方法，转化成DOM对象
div.style.color = 'red'; //操作dom对象的属性
```

其实我们翻开源码，看看就知道了，get方法就是利用的第一种方式处理的，只是包装成一个get让开发者更直接方便的使用。

#### DOM转jQuery

相比较jQuery转化成DOM，开发中更多的情况是把一个DOM对象加工成jQuery对象。`$(参数)`是一个多功能的方法，通过传递不同的参数而产生不同的作用。

> 如果传递给`$(DOM)`函数的参数是一个DOM对象，jQuery方法会把这个DOM对象给包装成一个新的jQuery对象

举个🌰：

HTML代码

```html
<div>元素一</div>
<div>元素二</div>
<div>元素三</div>
```

JavaScript代码

```js
var div = document.getElementsByTagName('div'); //dom对象
var $div = $(div); //jQuery对象
var $first = $div.first(); //找到第一个div元素
$first.css('color', 'red'); //给第一个元素设置颜色
```

对象是一个数组合集(3个`div`元素)。通过`$(div)`方法转化成jQuery对象，通过调用jQuery对象中的`first`与`css`方法查找第一个元素并且改变其颜色。

## jQuery选择器

### 基本选择器

|     选择器     |       描述       |                      注意                      |
| :------------: | :--------------: | :--------------------------------------------: |
|   `$('#id')`   |    获取元素id    | id是唯一的，每个id值在一个页面中只能使用一次。 |
| `$('.class')`  |   获取元素类名   |           将获取拥有这个类的元素合集           |
| `$('element')` | 获取元素html对象 |              将获取对应的元素合集              |
|    `$('*')`    |     全选择器     |              获取文档中所有的元素              |



### 层级选择器

|           选择器           |                             描述                             |                       注意                       |
| :------------------------: | :----------------------------------------------------------: | :----------------------------------------------: |
|   `$('parent > child')`    | **子选择器**：选择所有指定`parent`元素中指定的`child`的*直接子元素* |                                                  |
| `$('ancestor descendant')` | **后代选择器：**选择给定的祖先(ancestor)元素的所有后代(descendant)元素 | 一个元素的后代可能是子元素、孙子元素、曾孙元素等 |
|     `$('prev + next')`     |  **相邻兄弟选择器：**选择所有紧接在`prev`元素后的`next`元素  |               只用于选取相邻的元素               |
|   `$('prev ~ siblings')`   | **一般兄弟选择器：**匹配`prev`元素*之后*的所有`siblings`（兄弟元素） |                                                  |



### 基本筛选选择器

筛选选择器用`:`开头，用法与CSS中的**伪元素**相似，因此元素的某些属性也通过筛选器查找。

举个🌰：`$(':checked')`— 获取拥有`checked`属性的元素

|         选择器         |                             描述                             |
| :--------------------: | :----------------------------------------------------------: |
|     `$(':first')`      |                        匹配第一个元素                        |
|      `$(':last')`      |                       匹配最后一个元素                       |
| `$(':net(selector)')`  | **过滤选择器**，选择所有元素，同时去除给定的选择器(`selector`)元素 |
|   `$(':eq(index)')`    |           在匹配的集合中选择索引值为`index`的元素            |
|   `$(':gt(index)')`    |      选择匹配集合中所有大于给定`index`（索引值）的元素       |
|      `$(':even')`      |             选择索引值为偶数的元素，从0开始计数              |
|      `$(':odd')`       |             选择索引值为奇数的元素，从0开始计数              |
|   `$(':lt(index)')`    |       选择匹配集合中所有索引值小于给定index参数的元素        |
|     `$(':header')`     |               选择所有标题元素，像`h1,h2,h3`等               |
| `$(':lang(language)')` |              选择指定语言(`language`)的所有元素              |
|      `$(':root')`      |                      选择该文档的根元素                      |
|    `$(':animated')`    |                选择所有正在执行动画效果的元素                |



### 内容筛选选择器

|         选择器         |                     描述                     |
| :--------------------: | :------------------------------------------: |
| `$(':contains(text)')` |      选择所有包含指定文本(`text`)的元素      |
|     `$(':parent')`     |     选择所有**含有子元素或者文本**的元素     |
|     `$(':empty')`      | 选择所有**没有子元素**的元素（包含文本节点） |
| `$(':has(selector)')`  |    选择元素中**至少包含指定选择器**的元素    |



### 可见性筛选选择器

|     选择器      |        描述        |
| :-------------: | :----------------: |
| `$(':visible')` | 选择所有显示的元素 |
| `$(':hidden')`  | 选择所有隐藏的元素 |

> :hidden选择器，不仅仅包含样式是`display="none"`的元素，还包括隐藏表单、`visibility`等等。
>
> 说白了，如果元素**占据文档中一定的空间**，元素就被认为是可见的，否则就是隐藏元素。
>
> 有几种方式可以*不显示*一个元素：
>
> 1. CSS`display`的值是`none`
> 2. `type="hidden"`的表单元素
> 3. 宽度和高度都显式设置为0
> 4. 一个祖先元素是隐藏的，该元素是不会在页面上显示
> 5. CSS`visibility`的值是`hidden`
> 6. CSS`opacity`的值是0
>
> 但元素的`visibility:hidden`或`opacity:0`被认为是可见的，因为仍然占用空间布局。



### 属性筛选选择器

属性选择器让你可以**基于属性来定位一个元素**。

可以只指定该元素的某个属性，这样所有使用该属性的元素都将被定位。

|                   选择器                    |                             描述                             |                    注意                    |
| :-----------------------------------------: | :----------------------------------------------------------: | :----------------------------------------: |
|         `$("[attribute|='value']")`         | 选择**指定属性值等于给定字符串**或**以该文字串为前缀(该字符串后跟一个连字符"-")**的元素 | 例如`background-color`以`background`为前缀 |
|         `$("[attribute*='value']")`         | 选择**指定属性包含一个给定的子字符串**的元素(给定的属性包含某些值) |                   最实用                   |
|         `$("[attribute~='value']")`         |   选择**指定属性 *用空格分隔的值中* 包含一个给定值**的元素   |                                            |
|         `$("[attribute='value']")`          |                选择**指定属性是给定值**的元素                |                                            |
|         `$("[attribute!='value']")`         | 选择**不存在指定属性**，或**指定的属性值不等于给定值**的元素 |                                            |
|         `$("[attribute^='value']")`         |           选择**指定属性是以给定字符串开始**的元素           |                                            |
|         `$("[attribute$='value']")`         |             选择**指定属性是以给定值结尾**的元素             |           这个比较**区分大小写**           |
|             `$("[attribute]")`              |      选择**所有具有指定属性**的元素，该属性可以是任何值      |                   最实用                   |
| `$("[attributeFilter1][attributeFilterN]")` |            选择匹配**所有指定的属性筛选器**的元素            |                                            |

> **笔记：**
>
> - `[att=val]、[att]、[att|=val]、[att~=val]` 属于CSS 2.1规范
> - `[ns|attr]、[att^=val]、[att*=val]、[att$=val]` 属于CSS3规范
> - `[name!="value"]` 属于jQuery扩展的选择器
>
> `[attr="value"]`能帮我们定位不同类型的元素，特别是表单form元素的操作，比如说`input[type="text"],input[type="checkbox"]`等
>
> `[attr*="value"]`能在网站中帮助我们匹配不同类型的文件



### 子元素筛选选择器

子元素筛选选择器不常使用，其筛选规则比起其它的选择器稍微要复杂点

|          选择器           |                             描述                             |
| :-----------------------: | :----------------------------------------------------------: |
|    `$(':first-child')`    |          选择**所有父级元素**下的**第一个**子元素。          |
|    `$(':last-child')`     |          选择**所有父级元素**下的**最后一个**子元素          |
|    `$(':only-child')`     |   如果某个元素**是其父元素的唯一子元素**，那么它就会被选中   |
|   `$(':nth-child(n)')`    |             选择的**所有父元素的第`n`个子元素**              |
| `$(':nth-last-child(n)')` | 选择所有父元素的第`n`个子元素。**计数从最后一个元素开始到第一个**，即倒序查找 |



### 表单元素选择器

|      选择器      |                     描述                      |
| :--------------: | :-------------------------------------------: |
|  `$(':input')`   | 选择所有`input,textarea,select`和`button`元素 |
|   `$(':text')`   |                匹配所有文本框                 |
| `$(':password')` |                匹配所有密码框                 |
|  `$(':radio')`   |               匹配所有单选按钮                |
| `$(':checkbox')` |                匹配所有复选框                 |
|  `$(':submit')`  |               匹配所有提交按钮                |
|  `$(':image')`   |                匹配所有图像域                 |
|  `$(':reset')`   |               匹配所有重置按钮                |
|  `$(':button')`  |                 匹配所有按钮                  |
|   `$(':file')`   |                匹配所有文件域                 |

> **注意事项：**
>
> 除了`input`筛选选择器，几乎每个表单类别筛选器都对应一个`input`元素的`type`值。**大部分表单类别筛选器可以使用属性筛选器替换。**
>
> 比如 `$(':password') == $('[type=password]')`



### 表单对象属性筛选选择器

除了*表单元素选择器*外，*表单对象属性筛选选择器*也是专门针对表单元素的选择器，可以附加在其他选择器的后面，主要功能是**对所选择的表单元素进行筛选**

|      选择器      |            描述            |
| :--------------: | :------------------------: |
| `$(':enabled')`  |     选取可用的表单元素     |
| `$(':disabled')` |    选取不可用的表单元素    |
| `$(':checked')`  | 选取被选中的`<input>`元素  |
| `$(':selected')` | 选取被选中的`<option>`元素 |

> **注意事项：**
>
> 1. 选择器适用于复选框和单选框，对于下拉框元素, 使用 :selected 选择器
> 2. 在某些浏览器中，选择器`:checked`可能会错误选取到`<option>`元素，所以保险起见换用选择器`input:checked`，确保只会选取`<input>`元素



### 特殊选择器`this`

`this`，表示当前的上下文对象是一个`html`对象，可以调用`html`对象所拥有的属性和方法。

```js
p.addEventListener('click',function(){
    //this === p
    //以下两者的修改都是等价的
    this.style.color = "red";
    p.style.color = "red";
},false);
```

换成jQuery的做法：

`$(this)`，代表的上下文对象是一个`jQuery`的上下文对象，可以调用`jQuery`的方法和属性值。

```js
$('p').click(function(){
    //把p元素转化成jQuery的对象
    var $this= $(this) 
    $this.css('color','red')
})
```

## jQuery的属性与样式

