# 抽象工厂模式

> - **意图：**提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。
> - **主要解决：**主要解决接口选择的问题。
> - **何时使用：**系统的产品有多于一个的产品族，而系统只消费其中某一族的产品。
> - **如何解决：**在一个产品族里面，定义多个产品。
> - **关键代码：**在一个工厂里聚合多个同类产品。
> - **应用实例：**
>   - 工作了，为了参加一些聚会，肯定有两套或多套衣服吧，比如说有商务装（成套，一系列具体产品）、时尚装（成套，一系列具体产品），甚至对于一个家庭来说，可能有商务女装、商务男装、时尚女装、时尚男装，这些也都是成套的，即一系列具体产品。
>   - 假设一种情况（现实中是不存在的，要不然，没法进入共产主义了，但有利于说明抽象工厂模式），在您的家中，某一个衣柜（具体工厂）只能存放某一种这样的衣服（成套，一系列具体产品），每次拿这种成套的衣服时也自然要从这个衣柜中取出了。用 OOP 的思想去理解，所有的衣柜（具体工厂）都是衣柜类的（抽象工厂）某一个，而每一件成套的衣服又包括具体的上衣（某一具体产品），裤子（某一具体产品），这些具体的上衣其实也都是上衣（抽象产品），具体的裤子也都是裤子（另一个抽象产品）。
> - **优点：**当一个产品族中的多个对象被设计成一起工作时，它能保证客户端始终只使用同一个产品族中的对象。
> - **缺点：**产品族扩展非常困难，要增加一个系列的某一产品，既要在抽象的 Creator 里加代码，又要在具体的里面加代码。
> - **使用场景：** 
>   1. QQ 换皮肤，一整套一起换。 
>   2. 生成不同操作系统的程序。
> - **注意事项：**产品族难扩展，产品等级易扩展。

## 实例

1. 创建鞋类接口：

```c#
    /// <summary> 创建鞋类接口 </summary>
    public interface Shose
    {
        void wear();
    }
```

<br/>

创建鞋类实现接口的实体类

```c#
    /// <summary> 某踏 </summary>
    public class Anta : Shose
    {
        public void wear()
        {
            Console.WriteLine("I weared Anta!");
        }
    }
    /// <summary> 某迪 </summary>
    public class Adidas : Shose
    {
        public void wear()
        {
            Console.WriteLine("I weared Adidas!");
        }
    }
    /// <summary> 某丹 </summary>
    public class Jordan : Shose
    {
        public void wear()
        {
            Console.WriteLine("I weared Jordan!");
        }
    }
```

<br/>

2. 创建颜色接口

```c#
    public interface Colors
    {
        void fill();
    }
```

<br/>

创建颜色实现接口的实体类

```c#
    /// <summary> 姨妈红 </summary>
    public class Red : Colors
    {
        public void fill()
        {
            Console.WriteLine("The weared is Red!");
        }
    }

    /// <summary> 青草绿 </summary>
    public class Green : Colors
    {
        public void fill()
        {
            Console.WriteLine("The weared is Green!");
        }
    }

    /// <summary> 玄素蓝 </summary>
    public class Blue : Colors
    {
        public void fill()
        {
            Console.WriteLine("The weared is Blue!");
        }
    }
```

<br/>

3. 为 `Color` 和 `Shose` 对象创建抽象类 来获取工厂

```c#
    public abstract class AbstractFactory
    {
        public abstract Shose getShose(string shape);
        public abstract Colors getColor(string color);
    }
```

<br/>

4. 创建扩展了 `AbstractFactory` 的工厂类，基于给定的信息生成实体类的对象

```c#
    public class ShoseFactory : AbstractFactory
    {
        /// <summary> 造鞋厂 </summary>
        /// <param name="ShoseType">鞋子的品牌</param>
        /// <returns>返回鞋的对象</returns>
        public override Shose getShose(string ShoseType)
        {
            if (ShoseType == null) return null;

            if (ShoseType.Equals("Anta"))
            {
                return new Anta();
            }
            else if (ShoseType.Equals("Adidas"))
            {
                return new Adidas();
            }
            else if (ShoseType.Equals("Jordan"))
            {
                return new Jordan();
            }

            return null;
        }

        public override Colors getColor(string color)
        {
            return null;
        }
    }

    public class ColorsFactory : AbstractFactory
    {
        public override Shose getShose(string ShoseType)
        {
            return null;
        }

        /// <summary> 染色厂 </summary>
        /// <param name="color">颜色款式</param>
        /// <returns>返回颜色的对象</returns>
        public override Colors getColor(string color)
        {
            if (color == null) return null;

            if (color.Equals("Red"))
            {
                return new Red();
            }
            else if (color.Equals("Green"))
            {
                return new Green();
            }
            else if (color.Equals("Blue"))
            {
                return new Blue();
            }
            return null;
        }
    }
```

<br/>

5. 创建一个工厂生成器类，通过传递鞋类或颜色信息来获取工厂

```c#
    public class FactoryProducer
    {
        public static AbstractFactory getFactory(string choise)
        {
            if (choise.Equals("Shose"))
            {
                return new ShoseFactory();
            }
            else if (choise.Equals("Colors"))
            {
                return new ColorsFactory();
            }

            return null;
        }
    }
```

<br/>

6. 使用 `FactoryProducer` 来获取 `AbstractFactory`，通过传递类型信息来获取实体类的对象

```c#
    class Program
    {
        static void Main(string[] args)
        {
            //获取鞋类工厂
            AbstractFactory shoseFactory = FactoryProducer.getFactory("Shose");

            //穿鞋
            Shose shose1 = shoseFactory.getShose("Anta");
            shose1.wear();

            Shose shose2 = shoseFactory.getShose("Adidas");
            shose2.wear();

            Shose shose3 = shoseFactory.getShose("Jordan");
            shose3.wear();


            //获取颜色工厂
            AbstractFactory colorsFactory = FactoryProducer.getFactory("Colors");

            //上色
            Colors color1 = colorsFactory.getColor("Red");
            color1.fill();

            Colors color2 = colorsFactory.getColor("Green");
            color2.fill();

            Colors color3 = colorsFactory.getColor("Blue");
            color3.fill();
        }
    }
```

