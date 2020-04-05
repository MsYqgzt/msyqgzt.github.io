# WPF样式(Style)

## 基础样式

### 简单例子

```xaml
<Window x:Class="StyleTest.MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    xmlns:local="clr-namespace:StyleTest"
    xmlns:sys="clr-namespace:System;assembly=mscorlib"
    mc:Ignorable="d"

    <Window.Resources>
        <FontFamily x:Key="ButtonFontFamily">Times New Roman</FontFamily>
        <sys:Double x:Key="ButtonFontSize">16</sys:Double>
        <FontWeight x:Key="ButtonFontWeight">Bold</FontWeight>
    </Window.Resources>

    <StackPanel VerticalAlignment="Center">
        <Button Name="btn" Content="Resource Button"
                FontFamily="{StaticResource ButtonFontFamily}"
                FontSize="{StaticResource ButtonFontSize}"
                FontWeight="{StaticResource ButtonFontWeight}"/>
        </StackPanel>
</Window>
```

1. 为窗口添加三个资源
   1. `FontFamily`对象，包含希望使用的字体名称；
   2. 存储数字16的`double`对象（需要引用命名空间`xmlns:sys="clr-namespace:System;assembly=mscorlib"`）；
   3. 枚举值`FontWeightBold`（使用资源最常见的原因之一便是保存样式）
2. 在元素中直接使用这些资源，因为在应用程序的整个生命周期中，这些资源都不会发生变化，所以使用静态资源是合理的。
3. 最后就得到一个应用样式后的按钮。
4. 注：样式设置元素的**初始外观**，但可以随意覆盖它们设置的这些特性。若再在Button元素中明确设置`Fontsize="20"`，按钮标签中的`FontSize`设置会被覆盖为20。

### 改进结构

```xaml
<Window.Resources>
    <Style x:Key="ButtonStyle">
        <Setter Property="Control.FontFamily" Value="Times New Roman"/>
        <Setter Property="Control.FontSize" Value="16"/>
        <Setter Property="Control.FontWeight" Value="Bold"/>
    </Style>
</Window.Resources>
```

`xaml`设置方法：

```xaml
<StackPanel VerticalAlignment="Center">
    <Button Name="btn" Content="Resource Button"
            Style="{StaticResource ButtonStyle}"/>
</StackPanel>
```

**或**后台设置方法：

```c#
btn.Style = (Style)btn.FindResource("ButtonStyle");
```

- 上面的标记创建了一个独立资源：即`System.Windows.Style`对象。并包含三个`Setter`对象，每个`Setter`对象用于设置一个属性。并为该样式设置一个键名来引用样式。
- `Setter`对象中的`Property`设置针对`Control`类型，不只针对`Button`类型。
- 当它们都对控件的样式进行设置时，上个例子中只对`Button`控件有效果，而该例中对其他包含`FontFamily`、`FontSize`、`FontWeight`的控件都能有效果。

<br/>

除此以外我们还可以使用`TargetType`属性，限定该样式可以引用的对象，语法如下：

```xaml
<Window.Resources>
    <!-- 添加 TargetType="Button" -->
    <Style x:Key="ButtonStyle" TargetType="Button">
        <Setter Property="Control.FontFamily" Value="Times New Roman"/>
        <Setter Property="Control.FontSize" Value="16"/>
        <Setter Property="Control.FontWeight" Value="Bold"/>
    </Style>
</Window.Resources>
```

此例就实现了，**只允许**`Button`控件进行引用该样式。

## 关联事件处理

可以通过样式和后台事件的关联，实现动态样式的效果。

如：鼠标悬浮在文本标签上时，改变按钮的背景色。

```xaml
<Window.Resources>
    <Style x:Key="MouseOverHighlightStyle" TargetType="TextBlock">
        <Setter Property="HorizontalAlignment" Value="Center"/>
        <Setter Property="TextAlignment" Value="Center"/>
        <Setter Property="Padding" Value="5"/>
        <!-- 关联事件 -->
        <EventSetter Event="TextBlock.MouseEnter" Handler="element_MouseEnter"/>
        <EventSetter Event="TextBlock.MouseLeave" Handler="element_MouseLeave"/>
    </Style>
</Window.Resources>

<StackPanel>
    <TextBlock Text="This is a TextBlock"
               Style="{StaticResource MouseOverHighlightStyle}"/>
</StackPanel>
```

对应的后台代码：

```c#
void element_MouseEnter(object sender,MouseEventArgs e)
{
    ((TextBlock)sender).Background = new SolidColorBrush(Colors.Aqua);
}

void element_MouseLeave(object sender, MouseEventArgs e)
{
    ((TextBlock)sender).Background = null;
}
```

- `MouseEnter`和`MouseLeave`事件完成了背景颜色改变。
- 在`TextBlock`标签中，我们可以看到只需要应用一行`Style="{StaticResource MouseOverHighlightStyle}"`便可以实现功能，这非常适用于 当我们需要为大量元素应用鼠标悬停效果的情况下，基于样式的事件处理程序简化了这项任务。
- 但WPF中极少使用事件设置器这种技术，更方便使用的是**事件触发器**，它以声明的方式定义了所希望的行为，并且不需要任何代码。

## 多层样式（样式继承）

有时我们希望在另一个样式的基础上创建样式，这时可为样式设置`BasedOn`来使用此类样式继承。

```xaml
<Window.Resources>
    <Style x:Key="MouseOverHighlightStyle" TargetType="TextBlock">
        <Setter Property="HorizontalAlignment" Value="Center"/>
        <Setter Property="TextAlignment" Value="Center"/>
        <Setter Property="Padding" Value="5"/>
        <EventSetter Event="TextBlock.MouseEnter" Handler="element_MouseEnter"/>
        <EventSetter Event="TextBlock.MouseLeave" Handler="element_MouseLeave"/>
    </Style>

    <!-- 使用BasedOn继承样式 -->
    <Style x:Key="BaseOnStyle" TargetType="TextBlock"
           BasedOn="{StaticResource MouseOverHighlightStyle}">
        <Setter Property="Control.Foreground" Value="Red"/>
    </Style>
</Window.Resources>

<StackPanel VerticalAlignment="Center">
    <TextBlock Text="This is a TextBlock"
               Style="{StaticResource MouseOverHighlightStyle}"/>

    <TextBlock Text="Inherited Style TextBlock"
               Style="{StaticResource BaseOnStyle}"/>
</StackPanel>
```

第二个`TextBlock`继承了第一个`TextBlock`的样式，并在其基础上将文字颜色（`Foreground`）修改为了`Red`（红色）。

## 通过类型自动应用样式

当我们需要为界面的所有`Button`设置统一样式的时候，如果`Button`比较少，我们可以用上面的方法逐个设置样式，但是当`Button`非常多的时候，这样的方法就显得麻烦了。

这个时候我们就可以使用`TargetType`来**自动为对应的控件应用样式**。

```xaml
<Window.Resources>
    <Style x:Key="MouseOverHighlightStyle" TargetType="TextBlock">
        <Setter Property="HorizontalAlignment" Value="Center"/>
        <Setter Property="TextAlignment" Value="Center"/>
        <Setter Property="Padding" Value="5"/>
        <EventSetter Event="TextBlock.MouseEnter" Handler="element_MouseEnter"/>
        <EventSetter Event="TextBlock.MouseLeave" Handler="element_MouseLeave"/>
    </Style>

    <!-- 自动应用样式 -->
    <Style TargetType="{x:Type TextBlock}"
           BasedOn="{StaticResource MouseOverHighlightStyle}">
        <Setter Property="Control.Foreground" Value="Green"/>
    </Style>
</Window.Resources>

<StackPanel>
    <TextBlock Text="TextBlock1" Style="{StaticResource MouseOverHighlightStyle}"/>

    <TextBlock Text="TextBlock2" Style="{StaticResource BaseOnStyle}"/>

    <TextBlock Text="TextBlock3"/>

    <TextBlock Text="TextBlock4" Style="{x:Null}"/>
</StackPanel>
```

因为Style可以被覆盖，`TextBlock1`和`TextBlock2`为自己提供了一个新样式；而`TextBlock3`自动的应用了该样式；`TextBlock4`将Style属性设置为`null`值，这样就清除了样式。

## 触发器

$$
WPF触发器\begin{cases}
	简单触发器-Triggers\\
	多条件触发器-MultiTriggers\\
	事件触发器-EventTrigger\\
	数据触发器-DataTrigger
\end{cases}
$$

### 简单触发器-`Triggers`

> 满足简单的条件后触发

```xaml
<Window x:Class="Styles.SimpleTriggers" 
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" Title="SimpleTriggers" Height="300" Width="300">
    <Window.Resources>
        <Style x:Key="BigFontButton">
            <Style.Setters>
                <Setter Property="Control.FontFamily" Value="Times New Roman" />
                <Setter Property="Control.FontSize" Value="10" />
            </Style.Setters>

            <!-- 简单触发器 -->
            <Style.Triggers>
                <Trigger Property="Control.IsFocused" Value="True">
                    <Setter Property="Control.Foreground" Value="DarkRed" />
                </Trigger>
            </Style.Triggers>

        </Style>
    </Window.Resources>

    <StackPanel>
        <Button Style="{StaticResource BigFontButton}">
        A Customized Button
        </Button>
    </StackPanel>
</Window>
```

将一个`Button`赋予触发条件，当控件的`IsFocused`（成为焦点）属性值为`True`时触发，将这个`Button`的`Foreground`（前景色）赋值为`DarkRed`（深红色）

### 多条件触发器-`MultiTriggers`

> 满足多个条件时触发

```xaml
<MultiTrigger>
    <MultiTrigger.Conditions>
        <Condition Property="IsFocused" Value="True"></Condition>
        <Condition Property="Content" Value="{x:Null}"></Condition>
    </MultiTrigger.Conditions>
 
    <Setter Property="ToolTip" Value="content is null!"></Setter>
</MultiTrigger>
```

上述代码赋予多个触发条件，其含义是：

当对象同时满足条件

1. `IsFocused`（成为焦点）属性值为`True`时
2. `Content`(内容)属性值为`null`(空值)时

即将`ToolTip`（悬浮提示）属性值设置为`content is null!`

### 事件触发器-`EventTrigger`

> 在特定的路由事件发生时被触发，主要用于动画

```xaml
<Style TargetType="ListBoxItem">
    <Setter Property="Opacity" Value="0.5"/>
    <Setter Property="MaxHeight" Value="75"/>
    <Style.Triggers>
        <Trigger Property="IsSelected" Value="True">
            <Trigger.Setters>
                <Setter Property="Opacity" Value="1.0"/>
            </Trigger.Setters>
        </Trigger>

        <EventTrigger RoutedEvent="Mouse.MouseEnter">
            <EventTrigger.Actions>
                <BeginStoryboard>
                    <Storyboard>
                        <DoubleAnimation Duration="0:0:0.2" Storyboard.TargetProperty="MaxHeight" To="90"/>
                    </Storyboard>
                </BeginStoryboard>
            </EventTrigger.Actions>
        </EventTrigger>

        <EventTrigger RoutedEvent="Mouse.MouseLeave">
            <EventTrigger.Actions>
                <BeginStoryboard>
                    <Storyboard>
                        <DoubleAnimation Duration="0:0:1" Storyboard.TargetProperty="MaxHeight"/>
                    </Storyboard>
                </BeginStoryboard>
            </EventTrigger.Actions>
        </EventTrigger>

    </Style.Triggers>
</Style>
```



### 数据触发器-`DataTrigger`

> 根据绑定的数据不同，显示不同的内容

```xaml
<TreeView Name="_tree" Margin="0" BorderThickness="0" VerticalAlignment="Stretch" Background="Transparent" ItemsSource="{Binding Children}">
    <TreeView.ItemTemplate>
        <HierarchicalDataTemplate ItemsSource="{Binding Children}">
            <Border CornerRadius="0" Margin="1"
                    x:Name="back" MinWidth="70"
                    Background="Transparent"
                    DataContext="{Binding}"
                    PreviewMouseMove="TreeItem_PreviewMouseMove">
                <StackPanel Orientation="Horizontal" Margin="2">
                    <Image x:Name="BGimage"
                           Source="/Vdc3D.Coms.DModelEditor;component/Images/item.png"
                           Height="15" Width="15" />
                    <TextBlock Text="{Binding ShowText}" Margin="2 0"/>
                </StackPanel>
                <Border.ContextMenu>
                    <ContextMenu x:Name="menu">
                        <MenuItem Header="Add Directory"
                                  x:Name="menu_addDir"
                                  Click="AddDir_Click"
                                  DataContext="{Binding}"/>
                        <MenuItem Header="Add Property"
                                  x:Name="menu_addChild"
                                  Click="AddChild_Click"
                                  DataContext="{Binding}"/>
                        <MenuItem Header="Edit"
                                  Click="Modify_Click"
                                  DataContext="{Binding}"/>
                        <MenuItem Header="Delete"
                                  Click="Delete_Click"
                                  DataContext="{Binding}"/>
                    </ContextMenu>
                </Border.ContextMenu>
            </Border>
            <HierarchicalDataTemplate.Triggers>
                <MultiDataTrigger>
                    <MultiDataTrigger.Conditions>
                        <Condition Binding="{Binding RelativeSource={RelativeSource Mode=FindAncestor,AncestorType={x:Type TreeViewItem}},Path=IsExpanded}"
                                   Value="False"/>
                        <Condition Binding="{Binding IsDir}" Value="True"/>
                    </MultiDataTrigger.Conditions>
                    <MultiDataTrigger.Setters>
                        <Setter TargetName="BGimage"
                                Property="Source"
                                Value="/Vdc3D.Coms.DModelEditor;component/Images/dir.png" />
                    </MultiDataTrigger.Setters>
                </MultiDataTrigger>
                <MultiDataTrigger>
                    <MultiDataTrigger.Conditions>
                        <Condition Binding="{Binding RelativeSource={RelativeSource Mode=FindAncestor,AncestorType={x:Type TreeViewItem}},Path=IsExpanded}" Value="True"/>
                        <Condition Binding="{Binding IsDir}" Value="True"/>
                    </MultiDataTrigger.Conditions>
                    <MultiDataTrigger.Setters>
                        <Setter TargetName="BGimage"
                                Property="Source"
                                Value="/Vdc3D.Coms.DModelEditor;component/Images/dir_open.png" />
                    </MultiDataTrigger.Setters>
                </MultiDataTrigger>
                <DataTrigger Binding="{Binding IsDir}" Value="True">
                    <Setter TargetName="menu_addDir"
                            Property="Visibility"
                            Value="Visible"/>
                    <Setter TargetName="menu_addChild"
                            Property="Visibility"
                            Value="Visible"/>
                </DataTrigger>
                <DataTrigger Binding="{Binding IsDir}"
                             Value="False">
                    <Setter TargetName="menu_addDir"
                            Property="Visibility"
                            Value="Collapsed"/>
                    <Setter TargetName="menu_addChild"
                            Property="Visibility"
                            Value="Collapsed"/>
                </DataTrigger>
            </HierarchicalDataTemplate.Triggers>
        </HierarchicalDataTemplate>
    </TreeView.ItemTemplate>
</TreeView>
```

