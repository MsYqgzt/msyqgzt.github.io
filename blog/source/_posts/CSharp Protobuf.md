---
title: 在C#中使用Protocol buffer
tags: [编程]
categories: 
- [C#]
mathjax: true
date: 2021-02-24
---

## 准备工作

> 1. Visual Studio
> 2. NuGet安装:`Google.Protobuf`和`Google.Protobuf.Tools`
> 3. <span style="color:red">准备好`.proto`文件</span>



##  使用步骤

1. 找到`google.protobuf.tools`包所在目录下的`protoc.exe`文件
2. 在这个文件目录下创建`src`和`gen`文件夹
3. 将自己的`xx.proto`文件移动到`src`文件夹内
   - `.proto`文件之间可以互相引用，要正常使用，必须把所有相关的proto文件都准备好
4. 在`protoc.exe`文件所在目录打开命令行，执行以下命令：

```bash
.\protoc.exe --proto_path=src --csharp_out=gen xx.proto
```

5. 在`gen`文件夹中会产生等量的`.cs`文件，这就是对应的**解码器**，把它们导入自己的项目中
6. 在导入的解码器中已经具备了该`.proto`文件具备的函数。



## 官方🌰

proto的转换无非就是**类和二进制流相互转换**，颠过去倒回来。

官方提供的通讯录案例，实现了对于`Person`和`AddressBook`两个 message 的操作方法，这里仅翻译补充注释内容

```cs
using System;
using System.IO;

namespace Google.Protobuf.Examples.AddressBook
{
    internal class SampleUsage
    {
        private static void Main()
        {
            byte[] bytes;
            // 新建一个 person
            Person person = new Person
            {
                Id = 1,
                Name = "Foo",
                Email = "foo@bar",
                Phones = { new Person.Types.PhoneNumber { Number = "555-1212" } }
            };
            //
            using (MemoryStream stream = new MemoryStream())//方法一：通过内存流
            {
                // 保存 person 到二进制流
                person.WriteTo(stream);
                bytes = stream.ToArray();
            }
            Person copy = Person.Parser.ParseFrom(bytes);//将二进制流转换回 person 类
            
            //同理
            AddressBook book = new AddressBook
            {
                People = { copy }
            };
            bytes = book.ToByteArray();//方法二：直接用内部方法 将类转成二进制数组
            // 二进制数组 转到 类
            AddressBook restored = AddressBook.Parser.ParseFrom(bytes);
            // message 消息提供了等值比较的深度搜索:
            if (restored.People.Count != 1 || !person.Equals(restored.People[0]))
            {
                throw new Exception("There is a bad person in here!");
            }
        }
    }
}
```

