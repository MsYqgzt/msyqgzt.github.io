# 单例模式

> **意图：**保证一个类仅有一个实例，并提供一个访问它的全局访问点。
>
> **主要解决：**一个全局使用的类频繁地创建与销毁。
>
> **何时使用：**当您想控制实例数目，节省系统资源的时候。
>
> **如何解决：**判断系统是否已经有这个单例，如果有则返回，如果没有则创建。
>
> **关键代码：**构造函数是私有的。
>
> **应用实例：**
>
> 1. 一个班级只有一个班主任。
> 2. `Windows` 是多进程多线程的，在操作一个文件的时候，就不可避免地出现多个进程或线程同时操作一个文件的现象，所以所有文件的处理必须通过唯一的实例来进行。
> 3. 一些设备管理器常常设计为单例模式，比如一个电脑有两台打印机，在输出的时候就要处理不能两台打印机打印同一个文件。
>
> **优点：**
>
> 1. 在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如管理学院首页页面缓存）。
> 2. 避免对资源的多重占用（比如写文件操作）。
>
> **缺点：**没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。
>
> **使用场景：**
>
> 1. 要求生产唯一序列号。
> 2. WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来。
> 3. 创建的一个对象需要消耗的资源过多，比如 `I/O` 与数据库的连接等。
>
> **注意事项：**`getInstance() `方法中需要使用同步锁 `synchronized(Singleton.class) `防止多线程同时进入造成 instance 被多次实例化。

## 实例

### 懒汉式，线程不安全

> **描述：**这种方式是最基本的实现方式，这种实现最大的问题就是不支持多线程。因为没有加锁 synchronized，所以严格意义上它并不算单例模式。这种方式 lazy loading 很明显，不要求线程安全，在多线程不能正常工作。

```c#
using System;

namespace 单例模式
{
    class Lazy_unsave
    {
        //创建自身的一个对象
        private static Lazy_unsave instance;

        //让构造函数为 private，这样该类就不会被实例化
        private Lazy_unsave() { }

        //获取唯一可用的对象
        public static Lazy_unsave getInstance()
        {
            if (instance == null)
            {
                instance = new Lazy_unsave();
            }
            return instance;
        }

        public void output()
        {
            Console.WriteLine("Hello World!");
        }
    }
    
    class Program
    {
        static void Main(string[] args)
        {
            Lazy_unsave obj1 = Lazy_unsave.getInstance();
            obj1.output();

            return;
        }
    }
}

```



### 懒汉式，线程安全

> **描述：**这种方式具备很好的 lazy loading，能够在多线程中很好的工作，但是效率很低，99% 情况下不需要同步。
>
> - 优点：第一次调用才初始化，避免内存浪费
> - 缺点：
>   - 必须加锁 `synchronized` 才能保证单例，但加锁会影响效率
>   - `getInstance()` 的性能对应用程序不是很关键（该方法使用不太频繁）

```c#
using System;
using System.Runtime.CompilerServices;

namespace 单例模式
{
    class Lazy_save
    {
        private static Lazy_save instance;

        private Lazy_save() { }

        //获取唯一可用的对象
        [MethodImpl(MethodImplOptions.Synchronized)]
        public static Lazy_save getInstance()
        {
            if (instance == null)
            {
                instance = new Lazy_save();
            }
            return instance;
        }

        public void output()
        {
            Console.WriteLine("Hello World!");
        }
    }
    
    class Program
    {
        static void Main(string[] args)
        {
            Lazy_save obj2 = Lazy_save.getInstance();
            obj2.output();

            return;
        }
    }
}
```

<br/>

### 饿汉式

> **描述：**这种方式比较常用，但容易产生垃圾对象。
>
> - 优点：没有加锁，执行效率会提高。
> - 缺点：类加载时就初始化，浪费内存。

```c#
using System;

namespace 单例模式
{
    class Hungry
    {
        private static Hungry instance = new Hungry();
        private Hungry() { }
        public static Hungry getInstance()
        {
            return instance;
        }

        public void output()
        {
            Console.WriteLine("Hello Hunger!");
        }
    }
    
    class Program
    {
        static void Main(string[] args)
        {
            Hungry obj3 = Hungry.getInstance();
            obj3.output();

            return;
        }
    }
}

```

