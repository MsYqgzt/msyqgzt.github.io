---
title: Google Protocol buffers
tags: [编程]
categories: 
- [数据结构]
mathjax: true
date: 2021-02-23
---



## Protocol buffer 简介

`Protocol Buffer`是Google提出的一种支持多平台、多语言、可扩展的的数据序列化机制，相较于XML来说，`protobuf`更小更快更简单，支持自定义的数据结构，用`protobuf`编译器生成特定语言的源代码，如C++、Java、Python，目前`protobuf`对主流的编程语言都提供了支持,非常方便的进行序列化和反序列化。

[> Protocol Buffer官方文档](https://developers.google.com/protocol-buffers)



### Protocol Buffer 与 XML、JSON 的区别

- `Protocol Buffer` 和 `XML`、`JSON`一样都是*结构数据序列化*的工具，但它们的数据格式有比较大的区别：
  - 首先，Protocol Buffer 序列化之后得到的数据不是可读的字符串，而是**二进制流**
  - 其次，XML 和 JSON 格式的数据信息都包含在了序列化之后的数据中，不需要任何其它信息就能还原序列化之后的数据；但使用 Protocol Buffer 需要事先定义数据的格式(`.proto` 协议文件)，**还原**一个序列化之后的数据**需要使用到这个定义好的数据格式**
  - 最后，在传输数据量较大的需求场景下，`Protocol Buffer` 比 `XML`、`JSON` **更小（3到10倍）、更快（20到100倍）、使用 & 维护更简单**；而且 `Protocol Buffer` 可以跨平台、跨语音使用

## `.proto`文件语法

- `package`在Java里面代表这个文件所在的包名，在c#里面代表该文件的命名空间；
- `message`代表一个类；
- `required`代表该字段必填
- `optional`代表该字段可选，并可以为其设置默认值`[defalut=值]` 

### Message定义

一个搜索请求的`message`格式的简单🌰

```protobuf
syntax = "proto3";//定义proto版本

//message包含多个种类的fields
message SearchRequest {
	string query = 1;
	int32 page_number = 2;
	int32 result_per_page = 3;
}
```

上述例子中`fields`的种类都是数值型的（`string`和`int32`），当然也可以指定更加复杂的`fields`，比如枚举类型`enum`，或者是嵌套的`message`类型

#### 分配field编号

上述例子中每个`field`都被分配了一个编号，这个编号是这个`field`的唯一标识，其中需要注意的是，**标识1-15在编码的时候只占用一个字节，16-2047占用两个字节**，所以为了进一步优化我们的程序，对于那种经常出现的element，建议使用1-15来作为他们的唯一标识，对那些不被经常使用的element，建议使用16-2047，编号的范围是$1-2^{29}$（19000-19999是系统预留的，不要使用）

#### field类型

`message`当中的`field`类型包含以下两种（proto3）：

- singular

- repeated：该类型的field可以在message中重复使用（类似于数组），他们的顺序会被保存，通过索引进行检索，数值类型的repeated默认使用packed编码方式

#### 多message结构

在一个proto文件中可以定义多个`protobuf`

#### reserved field类型

在开发过程中可能会涉及到对proto文件中`message`各个fields的修改，可能是更新、删除某个field及其表示，这样可能会导致调用的服务失败。

其中一个防止这种问题的方式是，确保你要删除的`field`的标识（或是名字）是`reserved`，具体`protobuf`的编译器会决定未来这个field表示能否被使用

```protobuf
//数字标识和命名不能在同一条语句中混合声明
message Foo {
  reserved 2, 15, 9 to 11;
  reserved "foo", "bar";
}
```

#### 编译结果

使用`protoc`编译一个`.proto`文件

{% fold 这里是我个人的编译方式 %}

1. 找到`google.protobuf.tools`包所在目录下的`protoc.exe`文件
2. 在这个文件目录下创建`src`和`gen`文件夹
3. 将自己的`xx.proto`文件移动到`src`文件夹内
   - `.proto`文件之间可以互相引用，要正常使用，必须把所有相关的proto文件都准备好
4. 在`protoc.exe`文件所在目录打开命令行，执行以下命令：

```bash
.\protoc.exe --proto_path=src --csharp_out=gen xx.proto
```

5. 在`gen`文件夹中会产生等量的`.cs`文件，这就是对应的**解码器**，把它们导入自己的项目中

{% endfold %}

### 数据类型

具体的`proto`类型对应的生成类中的类型可参考[官方文档](https://developers.google.com/protocol-buffers/docs/proto3#scalar)

### 默认值

- 对于`string`和`byte`类型，默认值为空；
- 对于`bool`类型，默认值是`false`；
- 对于数值类型，默认值是0；
- 对于枚举类型，默认值是第一个枚举值，默认为0；
- 对于`message`类型，默认值由编程语言决定；
- 对于`repeated field`，默认值为空



### 枚举类型

当采用枚举类型之后，枚举中的值都是预先定义好的，对于上述例子，可以再额外增加一个枚举类型corpus，具体如下

```protobuf
message SearchRequest {
	string query = 1;
	int32 page_number = 2;
	int32 result_per_page = 3;
	enum Corpus {
	  UNIVERSAL = 0;
	  WEB = 1;
	  IMAGES = 2;
	  LOCAL = 3;
	  NEWS = 4;
	  PRODUCTS = 5;
	  VIDEO = 6;
	}
	Corpus corpus = 4;
  }
```

通常枚举类型的第一个值初始化为0，而且在`message`中使用枚举类型，**必须要给定一个为0的值，而且这个为0的值应该为第一个元素**

指定`option allow_alias = true;`来容许两个枚举元素有相同的值，即这两个元素能够相互替代

```protobuf
enum EnumAllowingAlias {
  option allow_alias = true;
  UNKNOWN = 0;
  STARTED = 1;
  RUNNING = 1;
}

enum EnumNotAllowingAlias {
  UNKNOWN = 0;
  STARTED = 1;
  // RUNNING = 1;//取消注释这一行，将导致Google内部出现编译错误，并在外部出现警告消息。
}
```

此外，枚举类型不仅可以定义在`message`内部，也可以定义在`message`外部，而且在不同message中可以重用`enum`。

在更改枚举类型`field`时，为保证系统运行正常，同样可以指定`reserved`数字标识和命名



### 使用其他message类型

#### 同文件引用

```protobuf
message SearchResponse {
  repeated Result results = 1;
}

message Result {
  string url = 1;
  string title = 2;
  repeated string snippets = 3;
}
```

#### 不同文件引用

引用其他proto文件中定义好的message类型

```protobuf
import "myproject/other_protos.proto";
```

有时我们会对引用的`proto`文件进行更改，比如将其内容移动到另外一个地方，这样我们就需要对调用方import路径进行更改，当调用方非常多的时候，这种方法是非常低效的。

`protobuf`提供一种机制，我们可以在原有位置提供一个新位置`proto`文件的“副本”，通过使用`import public`表示来实现，具体可以参考如下例子

```protobuf
// new.proto
// All definitions are moved here

// old.proto
// This is the proto that all clients are importing.
import public "new.proto";
import "other.proto";

// client.proto
import "old.proto";
// You use definitions from old.proto and new.proto, but not other.proto
```

这时编译器就会在某些固定目录下查询import的`proto`文件（具体在命令行编译的时候，由`-I/—proto_path`指定），如果上述路径找不到，编译器会在调用路径进行查找。通常将`—proto_path`设置为项目的根目录，然后import的时候使用完整的路径名

> proto2中的枚举类型无法直接使用

### 嵌套类型

具体如下

``` protobuf
message SearchResponse {
  message Result {
    string url = 1;
    string title = 2;
    repeated string snippets = 3;
  }
  repeated Result results = 1;
}
```

在parent`message`之外调用嵌套的`message`可以用如下方式：`SearchResponse.Result`，嵌套结构可以更加复杂，具体如下：

``` protobuf
message Outer {       // Level 0
  message MiddleAA {  // Level 1
    message Inner {   // Level 2
      int64 ival = 1;
      bool  booly = 2;
    }
  }
  message MiddleBB {  // Level 1
    message Inner {   // Level 2
      int32 ival = 1;
      bool  booly = 2;
    }
  }
}
```

### 更新message类型

当现有的`message`已经无法满足现有业务需要，你需要更新你的`message`类型以支持更复杂的业务，这就涉及到向后兼容的问题了，为保证已有服务不受影响，需要遵守以下的一些规定

1. 不要更改已经存在的`fields`的数字标识

2. 如果添加新的`field`，利用旧代码序列化得到的`message`可以使用新的代码进行解析，你需要记住各个元素的默认值。新代码创建的`field`同样可以由旧代码进行加解析

3. `field`可以被删除，但是需要保证其对应的数字标识不再被使用，你可以通过加前缀的方式来重新使用这个`field name`，或者指定数字标识为`reserved`来避免这种情况

4. `int32`、`int64`、`uint32`、`uint64`、`bool`这些类型都是互相兼容的，并不会影响前向、后向兼容性

5. `sint32`和`sint64`之间是互相兼容的，但是和其他数字类型是不兼容的

6. `string`和`bytes`是互相兼容的，只要使用的是`UTF-8`编码

7. 如果`byte`包含`message`的编码版本，则嵌套的`message`和`bytes`兼容

8. `flexed32`兼容`sfixed32`,`fixed64`,`sfixed64`

9. `enum`兼容`int32`，`uint32`，`int64`and `uint64`。对于这个值在转化时，不同语言的客户端处理方式会有所不同



参考资料：

1. [Protocol Buffer详解](https://zhuanlan.zhihu.com/p/95869546)