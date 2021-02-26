---
title: Protocol buffers 深入浅出♂
tags: [编程]
categories: 
- [数据结构]
mathjax: true
date: 2021-02-23
---



## Protocol buffer 简介

`Protocol Buffer`是Google提出的一种支持多平台、多语言、可扩展的的数据序列化机制，相较于XML来说，`protobuf`更小更快更简单，支持自定义的数据结构，用`protobuf`编译器生成特定语言的源代码，如C++、Java、Python，目前`protobuf`对主流的编程语言都提供了支持,非常方便的进行序列化和反序列化。

### Protocol Buffer 与 XML、JSON 的区别

- `Protocol Buffer` 和 `XML`、`JSON`一样都是*结构数据序列化*的工具，但它们的数据格式有比较大的区别：
  - 首先，Protocol Buffer 序列化之后得到的数据不是可读的字符串，而是**二进制流**
  - 其次，XML 和 JSON 格式的数据信息都包含在了序列化之后的数据中，不需要任何其它信息就能还原序列化之后的数据；但使用 Protocol Buffer 需要事先定义数据的格式(`.proto` 协议文件)，**还原**一个序列化之后的数据**需要使用到这个定义好的数据格式**
  - 最后，在传输数据量较大的需求场景下，`Protocol Buffer` 比 `XML`、`JSON` **更小（3到10倍）、更快（20到100倍）、使用 & 维护更简单**；而且 `Protocol Buffer` 可以跨平台、跨语言使用



### 它是如何工作的？

你可以通过在 `.proto` 文件中定义 protocol buffer message 类型，来指定你想如何对序列化信息进行结构化。每一个 protocol buffer message 是一个信息的小逻辑记录，包含了一系列的 键-值 对。这里有一个非常基础的 `.proto` 文件样例，它定义了一个包含 "person" 相关信息的 `message`：

```protobuf
message Person {
	required string name = 1;
	required int32 id = 2;
	optional string email = 3;
  
	enum PhoneType {
	  MOBILE = 0;
	  HOME = 1;
	  WORK = 2;
	}
  
	message PhoneNumber {
	  required string number = 1;
	  optional PhoneType type = 2 [default = HOME];
	}
  
	repeated PhoneNumber phone = 4;
}
```

正如你所见，message 格式很简单 - 每种`message`类型都有一个或多个具有唯一编号的字段，每个字段都有一个名称和一个值类型，其中值类型可以是数字（整数或浮点数），布尔值，字符串，原始字节，甚至（如上例所示）其它 protocol buffer message 类型，这意味着允许你分层次地构建数据。你可以指定 `optional` 字段，`required` 字段和 `repeated` 字段。 你可以在 [Protocol Buffer 语言指南](https://www.jianshu.com/p/6f68fb2c7d19) 中找到有关编写 `.proto` 文件的更多信息。

> proto3 已舍弃 required 字段，optional 字段也无法显示使用（因为缺省默认就设置为 optional）

一旦定义了 `messages`，就可以在 `.proto` 文件上运行 protocol buffer 编译器来生成指定语言的数据访问类。这些类为每个字段提供了简单的访问器（如 `name()`和 `set_name()`），以及将整个结构序列化为原始字节和解析原始字节的方法

例如，如果你选择的语言是 `C++`，则运行编译器上面的例子将生成一个名为 `Person` 的类。然后，你可以在应用程序中使用此类来填充，序列化和检索 `Person` 的 `messages`。于是你可以写一些这样的代码：

```c++
Person person;
person.set_name("John Doe");
person.set_id(1234);
person.set_email("jdoe@example.com");
fstream output("myfile", ios::out | ios::binary);
person.SerializeToOstream(&output);
```

之后，你可以重新读取解析你的 message

```c++
fstream input("myfile", ios::in | ios::binary);
Person person;
person.ParseFromIstream(&input);
cout << "Name: " << person.name() << endl;
cout << "E-mail: " << person.email() << endl;
```

你可以在 `message` 格式中添加新字段，而不会破坏向后兼容性；旧的二进制文件在解析时只是忽略新字段。因此，如果你的通信协议使用 protocol buffers 作为其数据格式，则可以扩展协议而无需担心破坏现有代码。
你可以在 [API 参考部分](https://developers.google.com/protocol-buffers/docs/reference/overview) 中找到使用生成的 protocol buffer 代码的完整参考，你可以在 [协议缓冲区编码](https://www.jianshu.com/p/82ff31c6adc6) 中找到更多关于如何对 protocol buffer messages 进行编码的信息。

## `proto3`语法指引

### 定义message

首先，让我们来看看一个非常简单的例子。假设你想要定义搜索请求消息格式，其中每个搜索请求都有**查询字符串**、你感兴趣的**结果的特定页面**以及每个**页面的多个结果**。以下是你用于定义消息类型的`.proto`文件。

```protobuf
syntax = "proto3";

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```

- 文件的第一行指定您使用的语法：如果您不这样做，协议缓冲器编译器将假设您正在使用[proto2](https://developers.google.com/protocol-buffers/docs/proto)。这必须是`proto3`文件的第一个非空、非注释行。
- 这个消息定义指定了三个字段（键/值对），包含在此类型（`SearchRequest`）的每个数据都指定一个字段在你的消息中。每个字段都有一个名称和类型。



### 指定字段类型

在上面的示例中，所有字段都是[标量类型](https://developers.google.com/protocol-buffers/docs/proto3#scalar)：两个整数（`page_number` `result_per_page`）和一个字符串（ `query`）。但你还可以为字段指定复合类型，包括[枚举类型](https://developers.google.com/protocol-buffers/docs/proto3#enum)和其他消息类型。



#### 分配字段编号

如你所见，message 定义中的每个字段都有**唯一编号**。这些数字以 [message 二进制格式](https://www.jianshu.com/p/82ff31c6adc6) 标识你的字段，并且一旦你的 message 被使用，这些编号就无法再更改。请注意，1 到 15 范围内的字段编号需要一个字节进行编码，编码结果将同时包含编号和类型（你可以在 [Protocol Buffer 编码](https://www.jianshu.com/p/82ff31c6adc6) 中找到更多相关信息）。16 到 2047 范围内的字段编号占用两个字节。因此，你应该为非常频繁出现的 message 元素保留字段编号 1 到 15。请记住为将来可能添加的常用元素预留出一些空间。

你可以指定的最小字段数为 1，最大字段数为 229   -  1 或 536,870,911。你也不能使用 19000 到 19999 范围内的数字（`FieldDescriptor::kFirstReservedNumber` 到 `FieldDescriptor::kLastReservedNumber`），因为它们是为 Protocol Buffers 的实现保留的 - 如果你使用这些保留数字之一，protocol buffer 编译器会抱怨你的 `.proto`。同样，你也不能使用任何以前定义的 [保留](https://developers.google.com/protocol-buffers/docs/proto#reserved) 字段编号。

> “不能使用任何以前定义的保留字段编号” 指的是使用 `reserved` 关键字声明的保留字段。



#### 指定字段规则

消息字段可以是以下信息字段之一：

- `singular`：一个设计良好的消息可以有0或1个此字段（但不超过1个）。这是 `proto3` 语法的默认字段规则。
- `repeated`: 该字段可以在格式良好的消息中重复任意多次（包括零）。其中重复值的顺序会被保留。

在 `proto3` 中，标量数字类型的 repeated 字段默认使用 `packed`编码。

你可以在 [Protocol Buffer 编码](https://www.jianshu.com/p/82ff31c6adc6) 中找到更多有关 `packed` 编码的信息。

> 对 required 的使用永远都应该非常小心。如果你希望在某个时刻停止写入或发送 required 字段，则将字段更改为可选字段将会有问题 - 旧读者会认为没有此字段的邮件不完整，可能会无意中拒绝或删除它们。你应该考虑为 buffers 编写特定于应用程序的自定义验证的例程。谷歌的一些工程师得出的结论是，使用 required 弊大于利；他们更喜欢只使用 optional 和 repeated。但是，这种观点并未普及。
>
> 在 proto3 中已经为兼容性彻底抛弃 required。



#### 添加更多message类型

可以在单个 `.proto` 文件中定义多种 消息类型。这在你需要定义多个相关 message 的时候会很有用。

- 例如，如果要定义与搜索请求相应的搜索回复 message - `SearchResponse` message，则可以将其添加到相同的 `.proto`文件：

```protobuf
message SearchRequest {
	required string query = 1;
  	optional int32 page_number = 2;
  	optional int32 result_per_page = 3;
}

message SearchResponse {
 	//...
}
```

> 组合 messages 会导致膨胀虽然可以在单个 .proto 文件中定义多种 messages 类型（例如 `message`，`enum` 和 `service`），但是当在单个文件中定义了大量具有不同依赖关系的 messages 时，它也会导致依赖性膨胀。建议每个 .proto 文件包含尽可能少的 message 类型。



#### Reserved 保留字段

如果你通过完全删除字段或将其注释掉来更新 message 类型，则未来一些用户在做他们的修改或更新时就可能会再次使用这些字段编号。如果以后加载相同 `.proto` 的旧版本，这可能会导致一些严重问题，包括数据损坏，隐私错误等。确保不会发生这种情况的一种方法是指定已删除字段的字段编号（有时也需要指定名称为保留状态，英文名称可能会导致 JSON 序列化问题）为 “保留” 状态。如果将来的任何用户尝试使用这些字段标识符，protocol buffer 编译器将会抱怨。

```protobuf
message Foo {
  	reserved 2, 15, 9 to 11;
	reserved "foo", "bar";
}
```

请注意，你不能在同一 "reserved" 语句中将字段名称和字段编号混合在一起指定。



#### 你的 .proto 文件将生成什么？

当你在 `.proto` 上运行 protocol buffer 编译器时，编译器将会生成所需语言的代码，这些代码可以操作文件中描述的 message 类型，包括获取和设置字段值、将 message 序列化为输出流、以及从输入流中解析出 message。

- 对于 **C++**，编译器从每个 `.proto` 生成一个 `.h` 和 `.cc` 文件，其中包含文件中描述的每种 message 类型对应的类。
- 对于 **Java**，编译器为每个 message 类型生成一个 `.java` 文件（类），以及用于创建 message 类实例的特殊 Builder 类。
- **Python** 有点不同 -  Python 编译器生成一个模块，其中包含 `.proto` 中每种 message 类型的静态描述符，然后与元类一起使用以创建必要的 Python 数据访问类。
- 对于 **Go**，编译器会生成一个 `.pb.go` 文件，其中包含对应每种 message 类型的类型。

- 对于 **Ruby，**编译器会生成包含您消息类型的 Ruby 模块的`.rb`文件。
- 对于 **Objective-C**，编译器从每个文件中生成一个`pbobjc.h`和一个`pbobjc.m`文件，并且为你的`.proto`文件中每个消息类型提供一个类。
- 对于 **C#，**编译器会从每个文件中生成一个`.cs`文件，对`.proto`文件中每个消息类型进行分类并描述。
- 对于 **Dart，**编译器会生成一个`.pb.dart`文件，为文件中的每种消息类型提供一个类。

 你可以按照所选语言的教程了解更多有关各种语言使用 API 的信息。有关更多 API 详细信息，请参阅相关的 [API 参考](https://developers.google.com/protocol-buffers/docs/reference/overview)。

### 标量值类型

标量 message 字段可以具有以下几种类型之一 - 该表显示 `.proto` 文件中指定的类型，以及自动生成的类中的相应类型：

| .proto Type |                            Notes                             | C++ Type | Java Type  | Python Type[2] | Go Type |           Ruby Type            |  C# Type   |     PHP Type      | Dart Type |
| :---------: | :----------------------------------------------------------: | :------: | :--------: | :------------: | :-----: | :----------------------------: | :--------: | :---------------: | :-------: |
|   double    |                                                              |  double  |   double   |     float      | float64 |             Float              |   double   |       float       |  double   |
|    float    |                                                              |  float   |   float    |     float      | float32 |             Float              |   float    |       float       |  double   |
|    int32    | 使用可变长度编码。编码负数的效率低 - 如果你的字段可能有负值，请改用 sint32 |  int32   |    int     |      int       |  int32  | Fixnum or Bignum (as required) |    int     |      integer      |    int    |
|    int64    | 使用可变长度编码。编码负数的效率低 - 如果你的字段可能有负值，请改用 sint64 |  int64   |    long    |  int/long[3]   |  int64  |             Bignum             |    long    | integer/string[5] |   Int64   |
|   uint32    |                       使用可变长度编码                       |  uint32  |   int[1]   |  int/long[3]   | uint32  | Fixnum or Bignum (as required) |    uint    |      integer      |    int    |
|   uint64    |                       使用可变长度编码                       |  uint64  |  long[1]   |  int/long[3]   | uint64  |             Bignum             |   ulong    | integer/string[5] |   Int64   |
|   sint32    | 使用可变长度编码。有符号的 int 值。这些比常规 int32 对负数能更有效地编码 |  int32   |    int     |      int       |  int32  | Fixnum or Bignum (as required) |    int     |      integer      |    int    |
|   sint64    | 使用可变长度编码。有符号的 int 值。这些比常规 int64 对负数能更有效地编码 |  int64   |    long    |  int/long[3]   |  int64  |             Bignum             |    long    | integer/string[5] |   Int64   |
|   fixed32   |    总是四个字节。如果值通常大于 228，则比 uint32 更有效。    |  uint32  |   int[1]   |  int/long[3]   | uint32  | Fixnum or Bignum (as required) |    uint    |      integer      |    int    |
|   fixed64   |    总是八个字节。如果值通常大于 256，则比 uint64 更有效。    |  uint64  |  long[1]   |  int/long[3]   | uint64  |             Bignum             |   ulong    | integer/string[5] |   Int64   |
|  sfixed32   |                         总是四个字节                         |  int32   |    int     |      int       |  int32  | Fixnum or Bignum (as required) |    int     |      integer      |    int    |
|  sfixed64   |                         总是八个字节                         |  int64   |    long    |  int/long[3]   |  int64  |             Bignum             |    long    | integer/string[5] |   Int64   |
|    bool     |                                                              |   bool   |  boolean   |      bool      |  bool   |      TrueClass/FalseClass      |    bool    |      boolean      |   bool    |
|   string    |       字符串必须始终包含 UTF-8 编码或 7 位 ASCII 文本        |  string  |   String   | str/unicode[4] | string  |         String (UTF-8)         |   string   |      string       |  String   |
|    bytes    |                     可以包含任意字节序列                     |  string  | ByteString |      str       | []byte  |      String (ASCII-8BIT)       | ByteString |      string       |   List    |

在 [Protocol Buffer 编码](https://www.jianshu.com/p/82ff31c6adc6) 中你可以找到有关序列化 message 时这些类型如何被编码的详细信息。

- [1] 在 Java 中，无符号的 32 位和 64 位整数使用它们对应的带符号表示，第一个 `bit` 位只是简单的存储在符号位中。
- [2] 在所有情况下，设置字段的值将执行类型检查以确保其有效。
- [3] 64 位或无符号 32 位整数在解码时始终表示为 `long`，但如果在设置字段时给出 `int`，则可以为`int`。在所有情况下，该值必须适合设置时的类型。见 [2]。
- [4] Python 字符串在解码时表示为 `Unicode`，但如果给出了 ASCII 字符串，则可以是 str（这条可能会发生变化）。

### 默认值

解析消息时，如果编码的消息不包含特定的单项元素，解析对象中的相应字段将设置为该字段的默认值。这些默认值是特定类型的：

- 对于字符串，默认值为空字符串。
- 对于字节，默认值为空字节。
- 对于胸部，默认值是false。
- 对于数字类型，默认值为0。
- 对于[枚举类型](https://developers.google.com/protocol-buffers/docs/proto3#enum)，默认值是**第一个定义的枚举值**，标号必须是0。
- 对于消息字段，未设置字段。它的实际值依据语言会有所不同。有关详细信息，请参阅[生成的代码指南](https://developers.google.com/protocol-buffers/docs/reference/overview)。

重复字段的默认值为空值（通常为适当语言中的空列表）。

请注意，对于 标量消息字段，一旦对消息进行解析，就无法确定字段是否被明确设置为默认值（例如布尔值是否设置为`false`）还是根本没有设置：在定义邮件类型时，你应该牢记这一点。

例如，如果你不希望该行为在默认情况下也发生，不要设置一个布尔值来开启某些行为。还要注意，如果标量消息字段设置为默认值，则不会在线上序列化该值

有关默认[在生成的代码](https://developers.google.com/protocol-buffers/docs/reference/overview)中如何工作的更多详细信息，请参阅所选语言生成的代码指南。



### 枚举 Enumerations

在定义 message 类型时，你可能希望其中一个字段只有一个预定义的值列表。例如，假设你要为每个 `SearchRequest` 添加语料库字段，其中语料库可以是 `UNIVERSAL`，`WEB`，`IMAGES`，`LOCAL`，`NEWS`，`PRODUCTS` 或 `VIDEO`。你可以通过向 message 定义添加枚举来简单地执行此操作 - 具有枚举类型的字段只能将一组指定的常量作为其值（如果你尝试提供不同的值，则解析器会将其视为一个未知的领域）。

在下面的例子中，我们添加了一个名为 `Corpus` 的枚举，其中包含所有可能的值，之后定义了一个类型为 Corpus 枚举的字段：

```protobuf
message SearchRequest {
  required string query = 1;
  optional int32 page_number = 2;
  optional int32 result_per_page = 3 [default = 10];
  enum Corpus {
    UNIVERSAL = 0;
    WEB = 1;
    IMAGES = 2;
    LOCAL = 3;
    NEWS = 4;
    PRODUCTS = 5;
    VIDEO = 6;
  }
  optional Corpus corpus = 4 [default = UNIVERSAL];
}
```

你可以通过为不同的枚举常量指定相同的值来定义别名。为此，你需要将 allow_alias 选项设置为true，否则 protocol 编译器将在找到别名时生成错误消息。

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
  // RUNNING = 1;  // 取消此行注释将导致 Google 内部的编译错误和外部的警告消息
}
```

枚举器常量必须在 32 位整数范围内。由于 `enum` 值在线上使用 [varint encoding](https://www.jianshu.com/p/82ff31c6adc6) ，负值效率低，因此不推荐使用。你可以在 message 中定义 `enums`，如上例所示的那样。或者将其定义在 message 外部 - 这样这些 `enum` 就可以在 `.proto` 文件中的任何 message 定义中重用。你还可以使用一个 message 中声明的 `enum` 类型作为不同 message 中字段的类型，使用语法 `MessageType.EnumType` 来实现。

当你在使用 `enum` 的 `.proto` 上运行 protocol buffer 编译器时，生成的代码将具有相应的用于　Java 或 C++ 的 `enum`，或者用于创建集合的 Python 的特殊 `EnumDescriptor` 类。运行时生成的类中具有整数值的符号常量。

有关如何在应用程序中使用 `enums` 的更多信息，请参阅相关语言的 [代码生成指南](https://developers.google.com/protocol-buffers/docs/reference/overview)

#### 保留值

如果你通过完全删除枚举条目或将其注释掉来更新枚举类型，则未来用户可能在对 message 做出自己的修改或更新时重复使用这些数值。如果以后加载相同 `.proto` 的旧版本，这可能会导致严重问题，包括数据损坏，隐私错误等。确保不会发生这种情况的一种方法是指定已删除字段的字段编号（有时也需要指定名称为保留状态，英文名称可能会导致 JSON 序列化问题）为 “保留” 状态。如果将来的任何用户尝试使用这些字段标识符，protocol buffer 编译器将会抱怨。你可以使用 `max` 关键字指定保留的数值范围一直到最大值。

```protobuf
enum Foo {
  reserved 2, 15, 9 to 11, 40 to max;
  reserved "FOO", "BAR";
}
```

请注意，你不能在同一 `reserved` 语句中将字段名称和字段编号混合在一起指定。



### 使用其他 Message 类型

你可以使用其他 message 类型作为字段类型。例如，假设你希望在每个 `SearchResponse` 消息中包含 `Result message` - 为此，你可以在同一 `.proto` 中定义 `Result message` 类型，然后在 `SearchResponse` 中指定 Result 类型的字段：

```protobuf
message SearchResponse {
  repeated Result result = 1;
}

message Result {
  required string url = 1;
  optional string title = 2;
  repeated string snippets = 3;
}
```

#### 导入定义 Importing Definitions

在上面的示例中，Result message 类型在与 `SearchResponse` 相同的文件中定义 - 如果要用作字段类型的 message 类型已在另一个 .proto 文件中定义，该怎么办？

你可以通过导入来使用其他 .proto 文件中的定义。要导入另一个 .proto 的定义，可以在文件顶部添加一个 import 语句：

```protobuf
import "myproject/other_protos.proto";
```

默认情况下，你只能使用直接导入的 .proto 文件中的定义。但是，有时你可能需要将 .proto 文件移动到新位置。现在，你可以在旧位置放置一个虚拟 .proto 文件，以使用 import public 概念将所有导入转发到新位置，而不是直接移动 .proto 文件并在一次更改中更新所有调用点。导入包含 import public 语句的 proto 的任何人都可以传递依赖导入公共依赖项。例如：

```protobuf
// new.proto
// All definitions are moved here
```

```protobuf
// old.proto
// This is the proto that all clients are importing.
import public "new.proto";
import "other.proto";
```

```protobuf
// client.proto
import "old.proto";
// 你可以使用 old.proto 和 new.proto 中的定义，但无法使用 other.proto
```

使用命令 -I/--proto_path 让 protocol 编译器在指定的一组目录中搜索要导入的文件。如果没有给出这个命令选项，它将查找调用编译器所在的目录。通常，你应将 --proto_path 设置为项目的根目录，并对所有导入使用完全限定名称。



### 使用 proto3 Message 类型

可以导入 [proto3](https://developers.google.com/protocol-buffers/docs/proto3) message 类型并在 proto2 message 中使用它们，反之亦然。但是，proto2 枚举不能用于 proto3 语法。



### 嵌套类型 Nested Types

你可以在其他 message 类型中定义和使用 message 类型，如下例所示 - 此处结果消息在`SearchResponse`消息中定义：

```protobuf
message SearchResponse {
  message Result {
    required string url = 1;
    optional string title = 2;
    repeated string snippets = 3;
  }
  repeated Result result = 1;
}
```

如果要在其父消息类型之外重用此消息类型，请将其称为 `Parent.Type`：

```protobuf
message SomeOtherMessage {
  optional SearchResponse.Result result = 1;
}
```

你可以根据需要深入的嵌套消息：

```protobuf
message Outer {                  // Level 0
  message MiddleAA {  // Level 1
    message Inner {   // Level 2
      required int64 ival = 1;
      optional bool  booly = 2;
    }
  }
  message MiddleBB {  // Level 1
    message Inner {   // Level 2
      required int32 ival = 1;
      optional bool  booly = 2;
    }
  }
}
```

### 更新 message 类型

如果现有的 message 类型不再满足你的所有需求 - 例如，你希望 message 格式具有额外的字段 - 但你仍然希望使用旧格式创建代码，请不要担心！在不破坏任何现有代码的情况下更新 message 类型非常简单。请记住以下规则：

- 请勿更改任何现有字段的字段编号。
- 你添加的任何新字段都应该是 `optional` 或 `repeated`。这意味着使用“旧”消息格式的代码序列化的任何消息都可以由新生成的代码进行解析，因为它们不会缺少任何 `required` 元素。你应该为这些元素设置合理的 `默认值`，以便新代码可以正确地与旧代码生成的 message 进行交互。同样，你的新代码创建的 message 可以由旧代码解析：旧的二进制文件在解析时只是忽略新字段。但是未丢弃这个新字段（未知字段），如果稍后序列化消息，则将新字段（未知字段）与其一起序列化 - 因此，如果将消息传递给新代码，则新字段仍然可用。
- 只要在更新的 message 类型中不再使用字段编号，就可以删除非必填字段。你可能希望重命名该字段，可能添加前缀 "OBSOLETE_"，或者将字段编号**保留（Reserved）**，以便将来你的 `.proto` 的用户不会不小心重用这个编号。
- 只要类型和编号保持不变，非必填字段就可以转换为扩展 [extensions](https://developers.google.com/protocol-buffers/docs/proto#extensions)，反之亦然。
- `int32`，`uint32`，`int64`，`uint64` 和 `bool` 都是兼容的 - 这意味着你可以将字段从这些类型更改为另一种类型，而不会破坏向前或向后兼容性。如果从中解析出一个不符合相应类型的数字，你将获得与在 C++ 中将该数字转换为该类型时相同的效果（例如，如果将 64 位数字作为 int32 读取，它将被截断为 32 位）。
- `sint32` 和 `sint64` 彼此兼容，但与其他整数类型不兼容。
- 只要字节是有效的 UTF-8，`string` 和 `bytes` 就是兼容的。
- 如果字节包含 message 的编码版本，则嵌入 message 与 `bytes` 兼容。
- `fixed32` 与 `sfixed32` 兼容，`fixed64` 与 `sfixed64` 兼容。
- `optional` 与 `repeated` 兼容。给定重复字段的序列化数据作为输入，期望该字段为 `optional` 的客户端将采用最后一个输入值（如果它是基本类型字段）或合并所有输入元素（如果它是 message 类型字段）。
- 更改默认值通常是正常的，只要你记住永远不会通过网络发送默认值。因此，如果程序接收到未设置特定字段的消息，则程序将看到该程序的协议版本中定义的默认值。它不会看到发件人代码中定义的默认值。
- `enum` 与 `int32`，`uint32`，`int64` 和 `uint64`兼容（注意，如果它们不适合，值将被截断），但要注意 message 反序列化时客户端代码对待它们将有所不同。值得注意的是，当 message 被反序列化时，将丢弃无法识别的 `enum` 值，这使得字段的 `has..` 访问器返回 false 并且其 getter 返回 `enum` 定义中列出的第一个值，或者如果指定了一个默认值则返回默认值。在 repeated 枚举字段的情况下，任何无法识别的值都将从列表中删除。但是，整数字段将始终保留其值。因此，在有可能接收超出范围的枚举值时，对整数升级为 `enum` 这一操作需要非常小心。
- 在当前的 Java 和 C++ 实现中，当删除无法识别的 `enum` 值时，它们与其他未知字段一起存储。请注意，如果此数据被序列化，然后由识别这些值的客户端重新解析，则会导致奇怪的行为。在 optional 可选字段的情况下，即使在反序列化原始 message 之后写入新值，旧值仍然可以被客户端识别。在 repeated 字段的情况下，旧值将出现在任何已识别和新添加的值之后，这意味着顺序将不被保留。
- 将单个 `optional` 值更改为 **new**`oneof` 的成员是安全且二进制兼容的。如果你确定没有代码一次设置多个，则将多个 `optional` 字段移动到新的 `oneof` 中可能是安全的。但是将任何字段移动到现有的 `oneof` 是不安全的。



### 扩展 Extensions

通过扩展，你可以声明 message 中的一系列字段编号用于第三方扩展。扩展名是那些未由原始 .proto 文件定义的字段的占位符。这允许通过使用这些字段编号来定义部分或全部字段从而将其它 .proto 文件定义的字段添加到当前 message 定义中。我们来看一个例子：

```protobuf
message Foo {
  // ...
  extensions 100 to 199;
}
```

这表示 Foo 中的字段数 [100,199] 的范围是为扩展保留的。其他用户现在可以使用指定范围内的字段编号在他们自己的 .proto 文件中为 Foo 添加新字段，例如：

```protobuf
extend Foo {
  optional int32 bar = 126;
}
```

这会将名为 bar 且编号为 126 的字段添加到 Foo 的原始定义中。

当用户的 Foo 消息被编码时，其格式与用户在 Foo 中常规定义新字段的格式完全相同。但是，在应用程序代码中访问扩展字段的方式与访问常规字段略有不同 - 生成的数据访问代码具有用于处理扩展的特殊访问器。那么，举个例子，下面就是如何在 C++ 中设置 bar 的值：

```c
Foo foo;
foo.SetExtension(bar, 15);
```

类似地，Foo 类定义模板化访问器 HasExtension()，ClearExtension()，GetExtension()，MutableExtension() 和 AddExtension()。它们都具有与正常字段生成的访问器相匹配的语义。有关使用扩展的更多信息，请参阅所选语言的代码生成参考。

请注意，扩展可以是任何字段类型，包括 message 类型，但不能是 oneofs 或 maps。



#### 嵌套扩展

你可以在另一种 message 类型内部声明扩展：

```protobuf
message Baz {
  extend Foo {
    optional int32 bar = 126;
  }
  ...
}
```

在这种情况下，访问此扩展的 C++ 代码为：

```c
Foo foo;
foo.SetExtension(Baz::bar, 15);
```

换句话说，唯一的影响是 bar 是在 Baz 的范围内定义。

> 注意:
>  这是一个常见的混淆源：在一个 message 类型中声明嵌套的扩展块并不意味着外部类型和扩展类型之间存在任何关系。特别是，上面的例子并不意味着 Baz 是 Foo 的任何子类。这意味着符号栏是在 Baz 范围内声明的；它仅仅只是一个静态成员而已。

一种常见的模式是在扩展的字段类型范围内定义扩展 - 例如，这里是 Baz 类型的 Foo 扩展，其中扩展名被定义为 Baz 的一部分：

```protobuf
message Baz {
  extend Foo {
    optional Baz foo_ext = 127;
  }
  ...
}
```

> 实际上就是要对某个 message A 扩展一个字段 B（B 类型），那么可以将这条扩展语句写在 message B 的定义里。

但是，并不是必须要在类型内才能定义该类型的扩展字段。你也可以这样做：

```protobuf
message Baz {
  ...
}

// 该定义甚至可以移到另一个文件中
extend Foo {
  optional Baz foo_baz_ext = 127;
}
```

实际上，这种语法可能是首选的，以避免混淆。

如上所述，嵌套语法经常被不熟悉扩展的用户误认为是子类。

#### 选择扩展字段编号

确保两个用户不使用相同的字段编号向同一 message 类型添加扩展名非常重要 - 如果扩展名被意外解释为错误类型，则可能导致数据损坏。你可能需要考虑为项目定义扩展编号的约定以防止这种情况发生。

如果你的编号约定可能涉及那些具有非常大字段编号的扩展，则可以使用 max 关键字指定扩展范围至编号最大值：

```protobuf
message Foo {
  extensions 1000 to max;
}
```

最大值为 229 - 1，或者 536,870,911。

与一般选择字段编号时一样，你的编号约定还需要避免 19000 到 19999 的字段编号(`FieldDescriptor::kFirstReservedNumber` 到 `FieldDescriptor::kLastReservedNumber`)，因为它们是为 Protocol Buffers 实现保留的。你可以定义包含此范围的扩展名范围，但 protocol 编译器不允许你使用这些编号定义实际扩展名。



### Oneof

如果你的 message 包含许多可选字段，并且最多只能同时设置其中一个字段，则可以使用 oneof 功能强制执行此行为并节省内存。

Oneof 字段类似于可选字段，除了 oneof 共享内存中的所有字段，并且最多只能同时设置一个字段。设置 oneof 的任何成员会自动清除所有其他成员。你可以使用特殊的 case() 或 WhichOneof() 方法检查 oneof 字段中当前是哪个值（如果有）被设置，具体方法取决于你选择的语言。

#### 使用 Oneof

要在 .proto 中定义 oneof，请使用 oneof 关键字，后跟你的 oneof 名称，在本例中为 test_oneof：

```protobuf
message SampleMessage {
  oneof test_oneof {
     string name = 4;
     SubMessage sub_message = 9;
  }
}
```

然后，将 oneof 字段添加到 oneof 定义中。你可以添加任何类型的字段，但不能使用 `required`，`optional` 或 `repeated` 关键字。如果需要向 oneof 添加重复字段，可以使用包含重复字段的 message。

在生成的代码中，oneof 字段与常规 `optional` 方法具有相同的 getter 和 setter。你还可以使用特殊方法检查 oneof 中的值（如果有）。你可以在相关的 API 参考中找到有关所选语言的 oneof API的更多信息。



#### Oneof 特性

- 设置 oneof 字段将自动清除 oneof 的所有其他成员。因此，如果你设置了多个字段，则只有你设置的最后一个字段仍然具有值。

```c
SampleMessage message;
message.set_name("name");
CHECK(message.has_name());
message.mutable_sub_message();   // Will clear name field.
CHECK(!message.has_name());
```

- 如果解析器遇到同一个 oneof 的多个成员，则在解析的消息中仅使用看到的最后一个成员。
- oneof 不支持扩展
- oneof 不能使用 repeated
- 反射 API 适用于 oneof 字段
- 如果你使用的是 C++，请确保你的代码不会导致内存崩溃。以下示例代码将崩溃，因为已通过调用 set_name() 方法删除了 sub_message。

```c
SampleMessage message;
SubMessage* sub_message = message.mutable_sub_message();
message.set_name("name");      // Will delete sub_message
sub_message->set_...            // Crashes here
```

- 同样在 C++中，如果你使用 Swap() 交换了两条 oneofs 消息，则每条消息将以另一条消息的 oneof 实例结束：在下面的示例中，msg1 将具有 sub_message 而 msg2 将具有 name。

  ```c
  SampleMessage msg1;
  msg1.set_name("name");
  SampleMessage msg2;
  msg2.mutable_sub_message();
  msg1.swap(&msg2);
  CHECK(msg1.has_sub_message());
  CHECK(msg2.has_name());
  ```



#### 向后兼容性问题

添加或删除其中一个字段时要小心。如果检查 oneof 的值返回 None/NOT_SET，则可能意味着 oneof 尚未设置或已设置为 oneof 的另一个字段。这种情况是无法区分的，因为无法知道未知字段是否是 oneof 成员。

#### 标签重用问题

- **将 optional 可选字段移入或移出 oneof**：在序列化和解析 message 后，你可能会丢失一些信息（某些字段将被清除）。但是，你可以安全地将单个字段移动到新的 oneof 中，并且如果已知只有一个字段被设置，则可以移动多个字段。
- **删除 oneof 字段并将其重新添加回去**：在序列化和解析 message 后，这可能会清除当前设置的 oneof 字段。
- **拆分或合并 oneof**：这与移动常规的 optional 字段有类似的问题。

### Maps

如果要在数据定义中创建关联映射，protocol buffers 提供了一种方便快捷的语法：

```cpp
map<key_type, value_type> map_field = N;
```

...其中 `key_type` 可以是任何整数或字符串类型（任何标量类型除浮点类型和 `bytes`）。请注意，枚举不是有效的 `key_type`。`value_type` 可以是除 map 之外的任何类型。

因此，举个例子，如果要创建项目映射，其中每个 "Project" message 都与字符串键相关联，则可以像下面这样定义它：

```protobuf
map<string, Project> projects = 3;
```

生成的 map API 目前可用于所有 proto2 支持的语言。你可以在相关的 [API 参考](https://developers.google.com/protocol-buffers/docs/reference/overview) 中找到有关所选语言的 map API 的更多信息。



#### Maps 特性

- maps 不支持扩展
- maps 不能是 repeated、optional、required
- map 值的格式排序和 map 迭代排序未定义，因此你不能依赖于特定顺序的 map 项
- 生成 .proto 的文本格式时，maps 按键排序。数字键按数字排序
- 当解析或合并时，如果有重复的 map 键，则使用最后看到的键。从文本格式解析 map 时，如果存在重复键，则解析可能会失败



#### 向后兼容性

map 语法等效于以下内容，因此不支持 map 的 protocol buffers 实现仍可处理你的数据：

```protobuf
message MapFieldEntry {
  optional key_type key = 1;
  optional value_type value = 2;
}

repeated MapFieldEntry map_field = N;
```

任何支持 maps 的 protocol buffers 实现都必须生成和接受上述定义所能接受的数据。



### Packages

你可以将 optional 可选的包说明符添加到 .proto 文件，以防止 protocol message 类型之间的名称冲突。

```protobuf
package foo.bar;
message Open { ... }
```

然后，你可以在定义 message 类型的字段时使用包说明符：

```protobuf
message Foo {
  ...
  required foo.bar.Open open = 1;
  ...
}
```

package 影响生成的代码的方式取决于你所选择的语言：

- 在 **C++** 中，生成的类包含在 C++ 命名空间中。例如，Open 将位于命名空间 foo::bar 中。
- 在 **Java** 中，除非在 .proto 文件中明确提供选项 java_package，否则该包将用作 Java 包
- 在 **Python** 中，package 指令被忽略，因为 Python 模块是根据它们在文件系统中的位置进行组织的

请注意，即使 package 指令不直接影响生成的代码，但是例如在 Python 中，仍然强烈建议指定 .proto 文件的包，否则可能导致描述符中的命名冲突并使 proto 对于其他语言不方便。



#### Packages 和名称解析

protocol buffer 语言中的类型名称解析与 C++ 类似：首先搜索最里面的范围，然后搜索下一个范围，依此类推，每个包被认为是其父包的 “内部”。一个领先的 '.'（例如 .foo.bar.Baz）意味着从最外层的范围开始。

protocol buffer 编译器通过解析导入的 .proto 文件来解析所有类型名称。每种语言的代码生成器都知道如何使用相应的语言类型，即使它具有不同的范围和规则。



### 定义服务

如果要将 message 类型与 RPC（远程过程调用）系统一起使用，则可以在 .proto 文件中定义 RPC 服务接口，protocol buffer 编译器将使用你选择的语言生成服务接口代码和存根。因此，例如，如果要定义一个 RPC 服务，其中具有一个获取 SearchRequest 并返回 SearchResponse 的方法，可以在 .proto 文件中定义它，如下所示：

```protobuf
service SearchService {
  rpc Search (SearchRequest) returns (SearchResponse);
}
```

默认情况下，protocol 编译器将生成一个名为 SearchService 的抽象接口和相应的 “存根” 实现。存根转发所有对 RpcChannel 的调用，而 RpcChannel 又是一个抽象接口，你必须根据自己的 RPC 系统自行定义。例如，你可以实现一个 RpcChannel，它将 message 序列化并通过 HTTP 将其发送到服务器。换句话说，生成的存根提供了一个类型安全的接口，用于进行基于 protocol-buffer 的 RPC 调用，而不会将你锁定到任何特定的 RPC 实现中。所以，在 C++ 中，你可能会得到这样的代码：

```c
using google::protobuf;

protobuf::RpcChannel* channel;
protobuf::RpcController* controller;
SearchService* service;
SearchRequest request;
SearchResponse response;

void DoSearch() {
  // You provide classes MyRpcChannel and MyRpcController, which implement
  // the abstract interfaces protobuf::RpcChannel and protobuf::RpcController.
  channel = new MyRpcChannel("somehost.example.com:1234");
  controller = new MyRpcController;

  // The protocol compiler generates the SearchService class based on the
  // definition given above.
  service = new SearchService::Stub(channel);

  // Set up the request.
  request.set_query("protocol buffers");

  // Execute the RPC.
  service->Search(controller, request, response, protobuf::NewCallback(&Done));
}

void Done() {
  delete service;
  delete channel;
  delete controller;
}
```

所有服务类还实现了 Service 接口，它提供了一种在编译时不知道方法名称或其输入和输出类型的情况下来调用特定方法的方法。在服务器端，这可用于实现一个可以注册服务的 RPC 服务器。

```c
using google::protobuf;

class ExampleSearchService : public SearchService {
 public:
  void Search(protobuf::RpcController* controller,
              const SearchRequest* request,
              SearchResponse* response,
              protobuf::Closure* done) {
    if (request->query() == "google") {
      response->add_result()->set_url("http://www.google.com");
    } else if (request->query() == "protocol buffers") {
      response->add_result()->set_url("http://protobuf.googlecode.com");
    }
    done->Run();
  }
};

int main() {
  // You provide class MyRpcServer.  It does not have to implement any
  // particular interface; this is just an example.
  MyRpcServer server;

  protobuf::Service* service = new ExampleSearchService;
  server.ExportOnPort(1234, service);
  server.Run();

  delete service;
  return 0;
}
```

如果你不想插入自己现有的 RPC 系统，现在可以使用 [gRPC](https://github.com/grpc/grpc-common): 一个由谷歌开发的与语言和平台无关的开源 RPC 系统。gRPC 特别适用于 protocol buffers，并允许你使用特殊的 protocol buffers 编译器插件直接从 `.proto` 文件生成相关的 RPC 代码。但是，由于使用 proto2 和 proto3 生成的客户端和服务器之间存在潜在的兼容性问题，我们建议你使用 proto3 来定义 gRPC 服务。你可以在 [Proto3 语言指南](https://www.jianshu.com/p/fc7485af828d) 中找到有关 proto3 语法的更多信息。如果你确实希望将 proto2 与 gRPC 一起使用，则需要使用 3.0.0 或更高版本的 protocol buffers 编译器和库。

除了 `gRPC` 之外，还有许多正在进行的第三方项目，用于开发 Protocol Buffers 的 RPC 实现。有关我们了解的项目的链接列表，请参阅 [第三方附加组件维基页面](https://github.com/google/protobuf/blob/master/docs/third_party.md)。





---

参考资料：

1. [Protocol Buffer官方文档](https://developers.google.com/protocol-buffers)
2. [[翻译] ProtoBuf 官方文档（一）- 开发者指南](https://www.jianshu.com/p/bdd94a32fbd1)