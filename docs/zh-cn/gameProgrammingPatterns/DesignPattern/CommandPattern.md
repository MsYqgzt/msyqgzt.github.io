# 命令模式

> **将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可撤销操作。**其本质是将命令的生产者和命令的消费者分开；**命令模式属于一种行为模式。**
>
> ——摘自*《游戏编程模式》*

## 举个🌰

### 角色移动

- 最普通的命令响应类，比如角色类，响应命令实现特定的动作

```c#
public class Actor
{
    public void Left()
    {
        Debug.Log("Left!");
    }
    public void Right()
    {
        Debug.Log("Right!");
    }
    // ...
}
```

- 引入抽象命令接口，调用者使用抽象命令接口编程

```c#
public abstract class Command
{
    public abstract void Execute(Actor actor);
}
```

- 具体命令类 需要实现上面的抽象接口，在实现中执行接收者对应的操作

```c#
public class LeftCommand : Command
{
    public override void Execute(Actor actor)
    {
        actor.Left();
    }
}
```

- 调用者 依据抽象命令接口编程，通过命令对象来执行请求

```c#
public class GameController
{
    public void Main()
    {
        Actor _actor = new Actor();
        Command _leftCommand = new LeftCommand();
        // execute
        _leftCommand.Execute(_actor);
    }
}
```

通过实现方式可以看出，命令的生产者和命令消费者之间已经没了直接调用关系，他们之间多了一层 `Command`。这样当你需要消费者执行不同的操作时，添加一个新的命令类并实现消费者的操作即可；甚至你的命令调用者能产生许多命令，将其`push`到一个命令队列中，角色只要从队列中读取命令响应，因为命令的响应已经与生产者无关了。

#### 加以改进

再回到之前的抽象命令接口。通过使用命令模式对 `ExecuteCommand` 方法加以改造，从而让代码更加优雅易懂。

```c#
void ExecuteCommand(Command command, Actor actor)
{
    command.Execute(actor);
}
```

现在，`ExecuteCommand` 接收的是一个 `Command` 和一个 `Actor`，这样看起来是不是更加符合我们的设计需求了?服务器模拟的 AI 的每一个操作通过一个个 `Command` 发送给客户端，客户端只需要执行命令命令就能够被消费。

`ExecuteCommand` 方法需要两个参数，在多数情况下这样是不错的选择。因为对于某个命令对象，你可以替换其中的操作执行者从而达到不同的执行者共享同一个命令对象的目的。但是如果碰到需要实现类似命令队列的问题，可能将每个操作执行者作为命令类的成员变量是更好的选择，但因此也就牺牲了共享命令类实例的优势。

### 撤销和重做

以上个实例的情景为基础，产生一系列命令的同时，将命令 push 到一个队列中，这样实现命令的撤销和恢复，尝试如下**改动**:

- 首先，将操作执行类作为命令类的成员变量

```c#
public abstract class Command
{
    protected Actor _actor;
    public abstract void Execute();
}
```

- 具体命令类做一下修改

```c#
public class LeftCommand : Command
{
  public LeftCommand(Actor actor)
  {
      _actor = actor;
  }
  public override void Execute()
  {
      _actor.Left();
  }
}
```

- 实现命令队列以及撤销、恢复操作

```c#
public class GameController
{
    //命令队列
    private List<Command> _commands = new List<Command>();
    //执行的命令位置
    private int _curCommandIdx = -1;
    
    public void Main()
    {
        ExecuteCommand(new LeftCommand(new Actor()));
        ExecuteCommand(new RightCommand(new Actor()));
        Undo();
        Do();
    }
    
    //执行指令
    void ExecuteCommand(Command command)
    {
        _curCommandIdx++;
        _commands.Add(command);
        _commands[_curCommandIdx].Execute();
    }
    //撤销指令
    void Undo()
    {
        _curCommandIdx--;
        _commands[_curCommandIdx].Execute();
    }
    //重做指令
    void Do()
    {
        _curCommandIdx++;
        _commands[_curCommandIdx].Execute();
    }
}
```

以上就是命令队列简单的实现，虽然牺牲了共享命令类实例的优势，但是换回了更多的设计需求。

所以在有些情况下，当操作非常多的时候，就需要写更多的具体命令类(虽然本来的目的就如此)，当不能共享命令类(如同上面的命令队列实现)就可能会产生大量的命令实例。所以，最终的权衡还是要根据更多的需求实际情况来确定。