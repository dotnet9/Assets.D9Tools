---
title: 如何在没有第三方.NET库源码的情况，调试第三库代码？
slug: How-to-be-without-a-third-party-dotNET-library-source-code-debugging-the-third-library-code
description: 借助dnSpy调试第三方库代码
date: 2023-09-21 22:00:19
copyright: Default
draft: false
cover: https://img1.dotnet9.com/2023/09/cover_06.gif
categories: .NET相关
---

大家好，我是沙漠尽头的狼。

本方首发于[Dotnet9](https://dotnet9.com/2023/09/How-to-be-without-a-third-party-dotNET-library-source-code-debugging-the-third-library-code)，介绍使用dnSpy调试第三方.NET库源码，行文目录：

- 安装dnSpy
- 编写示例程序
- 调试示例程序
- 调试.NET库原生方法
- 总结

## 1. 安装dnSpy

dnSpy是一款功能强大的.NET程序反编译工具，可以对.NET程序进行反编译，代替库文档的功能，代码丢失或者损坏可直接恢复，所以能在完全没有源码的情况下即时调试程序，甚至还能修改程序！

Github有二进制可执行程序下载地址，也有源码可以自行编译，本文使用前者，Github地址是：https://github.com/dnSpy/dnSpy

![](https://img1.dotnet9.com/2023/09/0601.png)

## 2. 编写示例程序

示例为一个桌面程序，输入数字，右侧回显输入的数字是奇数还是偶数：

![](https://img1.dotnet9.com/2023/09/0602.gif)

示例代码比较简单，界面绑定和ViewModel关系截图看完所有：

![](https://img1.dotnet9.com/2023/09/0603.png)

奇偶判断由类`TestTool`的`TellMeOddEven`方法返回，再回看回显，咦，0是奇数？1是偶数？

`TestTool`类是其他库定义，我假装你没有源码哈，虽然你有：

![](https://img1.dotnet9.com/2023/09/0604.png)

类具体定义如下：

```csharp
namespace TestDll;

public class TestTool
{
    public string TellMeOddEven(int number)
    {
        if (number % 2 == 1)
        {
            return $"{number}是偶数";
        }

        return $"{number}是奇数";
    }
}
```

## 3. 调试示例程序

打开dnSpy，将主程序引用的`TestDll`拖入：

![](https://img1.dotnet9.com/2023/09/0605.gif)

可以看到反编译后的代码：

![](https://img1.dotnet9.com/2023/09/0606.png)

反编译出来的方法定义会和第三方源码可能不同，以下是一些可能导致不同反编译结果的因素：

1. 编译器优化：不同版本的编译器可能会对代码进行不同的优化，例如使用不同的算法、数据结构或者代码重排等。这些优化可能会导致反编译出来的代码结构和顺序不同，本文示例使用 .NET 8开发，.NET Framework编译的库可能反编译出来与源码几乎一致。

2. 反编译工具更新：dnSpy本身也会不断更新，以适应新的.NET版本和编译器特性。这些更新可能会改变反编译算法和策略，从而导致不同版本的dnSpy反编译结果不一致。

代码简单，对比源码和反编译的代码查看，对整型入参除2取余，如果等于1判断为偶数，否则为奇数，当然这是错的，假如代码逻辑复杂，可以用dnSpy调试。

运行测试程序，并在dnSpy中给方法打断点，在调试菜单附加测试程序，就和VS中操作类似：

![](https://img1.dotnet9.com/2023/09/0607.gif)

## 4. 调试.NET库方法

上面调试示例程序的方法可用于其他第三方.NET库，那么.NET自身库方法呢？

方法类似，找到.NET库对应类、对应方法，运行目标程序，然后打断点。.NET库方法这样找：点击【文件】》【从GAC打开】=》搜索目标库，双击库，再查找目标方法，后面调试步骤就是一样了：

![](https://img1.dotnet9.com/2023/09/0608.gif)

## 5. 总结

- 技术交流加群请添加站长微信号：dotnet9com
- 文中示例代码：[MultiVersionLibrary](https://github.com/dotnet9/TerminalMACS.ManagerForWPF/tree/master/src/Demo/MultiVersionLibrary)


dnSpy很强大的，还能直接监视第三方代码的变量、修改值等，就和你使用VS开发自己的程序一样，了解更多用法还请查看文章开头给的链接https://github.com/dnSpy/dnSpy， 这篇大佬的文章也不错，建议看看：[《神器如 dnSpy，无需源码也能修改 .NET 程序》](https://blog.csdn.net/WPwalter/article/details/80457131)。

**对了，示例程序中奇偶数判断不对，我又没代码我想纠正怎么办？**

解决这个问题，上面大佬的文章您可以拜读了，下一篇站长继续讲解第三方库拦截，能实现不修改第三方库达到修改方法逻辑和返回结果的效果，可以提前预习[快学会这个技能-.NET API拦截技法](https://dotnet9.com/2023/02/Learn-this-skill-Dotnet-API-interception-technique)，当然下一篇会有新知识点：非公有类非公有方法拦截技法。

以原仓库两张dnSpy调试第三方库的动图结束本文：

![](https://img1.dotnet9.com/2023/09/cover_06.gif)

![](https://img1.dotnet9.com/2023/09/0609.gif)