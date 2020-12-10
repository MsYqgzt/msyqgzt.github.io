---
title: WPF 实现阻塞窗体
tags: [编程]
categories: 
- [C#]
- [wpf]
date: 2020-12-02
---



## 实现`MessageBox`的阻塞弹窗功能

弹出对话框`Show()`方法：

```c#
DispatcherFrame _dispatcherFrame;
/// <summary> 显示提示消息框，parent指定所属父元素 </summary>
public bool Show (string msg, string title, Grid parent) {
    var res = true;
    Application.Current.Dispatcher.Invoke (new Action (() => {
        MessageBox msgbox = new MessageBox (msg, title, parent);
        parent.Children.Add (msgbox);

        try //阻塞窗体其他内容
        {
            ComponentDispatcher.PushModal (); //入栈
            _dispatcherFrame = new DispatcherFrame (true);
            Dispatcher.PushFrame (_dispatcherFrame);
        } finally { ComponentDispatcher.PopModal (); } //出栈

        res = msgbox.Result;
    }));
    return res;
}
```



对话框内按钮的交互：

```c#
void OK_Click (object sender, RoutedEventArgs e) {
    Result = true;

    // 当关闭终止消息循环
    if (_dispatcherFrame != null) {
        _dispatcherFrame.Continue = false;
    }
    // 这里必须强制调用一下
    // 否则出现同时点开多个窗口时，只有一个窗口让代码继续
    ComponentDispatcher.PopModal ();

    UserControl_Unloaded (sender, e);
    //((Grid)Parent).Children.Remove(this);
    e.Handled = true;
}

void Cancel_Click (object sender, RoutedEventArgs e) {
    Result = false;

    // 当关闭终止消息循环
    if (_dispatcherFrame != null) {
        _dispatcherFrame.Continue = false;
    }
    // 这里必须强制调用一下
    // 否则出现同时点开多个窗口时，只有一个窗口让代码继续
    ComponentDispatcher.PopModal ();

    UserControl_Unloaded (sender, e);

    e.Handled = true;
}
```

