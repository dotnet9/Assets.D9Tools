---
title: C# 10的新特性
slug: New-features-of-csharp-10
description: 我们很高兴地宣布 C# 10 作为 .NET 6 和 Visual Studio 2022的一部分已经发布了。
date: 2022-02-12 10:14:31
copyright: Reprinted
author: 微软中国MSDN
originaltitle: C# 10的新特性
originallink: https://mp.weixin.qq.com/s/zLwo05uQOTXg_f2dwgRP-w
draft: False
cover: https://img1.dotnet9.com/2022/02/cover_03.jpg
categories: .NET相关
tags: .NET 6,C# 10
---

（本文阅读所需15分钟）

我们很高兴地宣布 C# 10 作为 .NET 6 和 Visual Studio 2022的一部分已经发布了。在这篇文章中，我们将介绍 C# 10 的许多新功能，这些功能使您的代码更漂亮、更具表现力、更快。

阅读 Visual Studio 2022 公告和.NET 6 公告以了解更多信息，包括如何安装。

- [Visual Studio 2022 公告](https://aka.ms/vs2022gablog)
- [.NET 6](https://aka.ms/dotnet6-GA)

## 全局和隐式 usings 

using 指令简化了您使用命名空间的方式。C# 10 包括一个新的全局 using 指令和隐式 usings，以减少您需要在每个文件顶部指定的 usings 数量。

### 全局 using 指令

如果关键字 global 出现在 using 指令之前，则 using 适用于整个项目：

```C#
global using System;
```

您可以在全局 using 指令中使用 using 的任何功能。例如，添加静态导入类型并使该类型的成员和嵌套类型在整个项目中可用。如果您在using 指令中使用别名，该别名也会影响您的整个项目：

```C#
global using static System.Console;
global using Env = System.Environment;
```

您可以将全局使用放在任何 .cs 文件中，包括 Program.cs 或专门命名的文件，如 globalusings.cs。全局 usings 的范围是当前编译，一般对应当前项目。

有关详细信息，请参阅全局 using 指令。

- [全局 using 指令](https://docs.microsoft.com/dotnet/csharp/languagereference/keywords/using-directive#global-modifier)

### 隐式 usings

隐式 usings 功能会自动为您正在构建的项目类型添加通用的全局 using 指令。要启用隐式 usings，请在 .csproj 文件中设置 ImplicitUsings 属性：

```xml
<PropertyGroup>
    <!-- Other properties like OutputType and TargetFramework -->
    <ImplicitUsings>enable</ImplicitUsings>
</PropertyGroup>
```

在新的 .NET 6 模板中启用了隐式 usings 。在此博客文章中阅读有关 .NET 6 模板更改的更多信息。 

一些特定全局 using 指令集取决于您正在构建的应用程序的类型。例如，控制台应用程序或类库的隐式 usings 不同于 ASP.NET 应用程序的隐式 usings。
有关详细信息，请参阅此隐式usings文章。

- [博客文章](https://devblogs.microsoft.com/dotnet/announcing-net-6-preview-7/#net-sdk-c-project-templates-modernized)
- [隐式usings](https://docs.microsoft.com/en-us/dotnet/core/project-sdk/overview#implicit-using-directives)

## Combining using 功能 

文件顶部的传统 using 指令、全局 using 指令和隐式 using 可以很好地协同工作。隐式 using 允许您在项目文件中包含适合您正在构建的项目类型的 .NET 命名空间。全局 using 指令允许您包含其他命名空间，以使它们在整个项目中可用。代码文件顶部的 using 指令允许您包含项目中仅少数文件使用的命名空间。

无论它们是如何定义的，额外的 using 指令都会增加名称解析中出现歧义的可能性。如果遇到这种情况，请考虑添加别名或减少要导入的命名空间的数量。例如，您可以将全局 using 指令替换为文件子集顶部的显式 using 指令。

如果您需要删除通过隐式 usings 包含的命名空间，您可以在项目文件中指定它们：

```xml
<ItemGroup>
  <Using Remove="System.Threading.Tasks" />
</ItemGroup>
```

您还可以添加命名空间，就像它们是全局 using 指令一样，您可以将 Using 项添加到项目文件中，例如：

```xml
<ItemGroup>
  <Using Include="System.IO.Pipes" />
</ItemGroup>
```

## 文件范围的命名空间 

许多文件包含单个命名空间的代码。从 C# 10 开始，您可以将命名空间作为语句包含在内，后跟分号且不带花括号：

```C#
namespace MyCompany.MyNamespace;

class MyClass // Note: no indentation
{ ... } 
```

他简化了代码并删除了嵌套级别。只允许一个文件范围的命名空间声明，并且它必须在声明任何类型之前出现。

有关文件范围命名空间的更多信息，请参阅命名空间关键字文章。

- [命名空间关键字文章](https://docs.microsoft.com/dotnet/csharp/languagereference/keywords/namespace)

## 对 lambda 表达式和方法组的改进 

我们对 lambda 的语法和类型进行了多项改进。我们预计这些将广泛有用，并且驱动方案之一是使 ASP.NET Minimal API 更加简单。

- [lambda 的语法](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-10#lambda-expression-improvements)
- [ASP.NET Minimal API](https://devblogs.microsoft.com/dotnet/announcing-asp-net-core-in-net-6/)

### lambda 的自然类型

Lambda 表达式现在有时具有“自然”类型。这意味着编译器通常可以推断出 lambda 表达式的类型。

到目前为止，必须将 lambda 表达式转换为委托或表达式类型。在大多数情况下，您会在 BCL 中使用重载的 Func<...> 或 Action<...> 委托类型之一：

```C#
Func<string, int> parse = (string s) => int.Parse(s);
```

但是，从 C# 10 开始，如果 lambda 没有这样的“目标类型”，我们将尝试为您计算一个：

```C#
var parse = (string s) => int.Parse(s);
```

您可以在您最喜欢的编辑器中将鼠标悬停在 var parse 上，然后查看类型仍然是 Func<string, int>。一般来说，编译器将使用可用的 Func 或 Action 委托（如果存在合适的委托）。否则，它将合成一个委托类型（例如，当您有 ref 参数或有大量参数时）。

并非所有 lambda 表达式都有自然类型——有些只是没有足够的类型信息。 例如，放弃参数类型将使编译器无法决定使用哪种委托类型：

```C#
var parse = s => int.Parse(s); // ERROR: Not enough type info in the lambda
```

lambda 的自然类型意味着它们可以分配给较弱的类型，例如 object 或 Delegate：

```C#
object parse = (string s) => int.Parse(s);   // Func<string, int>
Delegate parse = (string s) => int.Parse(s); // Func<string, int>
```

当涉及到表达式树时，我们结合了“目标”和“自然”类型。如果目标类型是LambdaExpression 或非泛型 Expression（所有表达式树的基类型）并且 lambda 具有自然委托类型 D，我们将改为生成 Expression<D>：

```C#
LambdaExpression parseExpr = (string s) => int.Parse(s); // Expression<Func<string, int>>
Expression parseExpr = (string s) => int.Parse(s);       // Expression<Func<string, int>>
```

### 方法组的自然类型

方法组（即没有参数列表的方法名称）现在有时也具有自然类型。您始终能够将方法组转换为兼容的委托类型：

```C#
Func<int> read = Console.Read;
Action<string> write = Console.Write;
```

现在，如果方法组只有一个重载，它将具有自然类型：

```C#
var read = Console.Read; // Just one overload; Func<int> inferred
var write = Console.Write; // ERROR: Multiple overloads, can't choose
```

### lambda 的返回类型

在前面的示例中，lambda 表达式的返回类型是显而易见的，并被推断出来的。情况并非总是如此：

```C#
var choose = (bool b) => b ? 1 : "two"; // ERROR: Can't infer return type
```

在 C# 10 中，您可以在 lambda 表达式上指定显式返回类型，就像在方法或本地函数上一样。返回类型在参数之前。当你指定一个显式的返回类型时，参数必须用括号括起来，这样编译器或其他开发人员不会太混淆：

```C#
var choose = object (bool b) => b ? 1 : "two"; // Func<bool, object>
```

### lambda 上的属性

从 C# 10 开始，您可以将属性放在 lambda 表达式上，就像对方法和本地函数一样。当有属性时，lambda 的参数列表必须用括号括起来：

```C#
Func<string, int> parse = [Example(1)] (s) => int.Parse(s);
var choose = [Example(2)][Example(3)] object (bool b) => b ? 1 : "two";
```

就像本地函数一样，如果属性在 AttributeTargets.Method 上有效，则可以将属性应用于 lambda。

Lambda 的调用方式与方法和本地函数不同，因此在调用 lambda 时属性没有任何影响。但是，lambdas 上的属性对于代码分析仍然有用，并且可以通过反射发现它们。

### structs 的改进

C# 10 为 structs 引入了功能，可在 structs (结构)和类之间提供更好的奇偶性。这些新功能包括无参数构造函数、字段初始值设定项、记录结构和 with 表达式。

#### 01 无参数结构构造函数和字段初始值设定项

在 C# 10 之前，每个结构都有一个隐式的公共无参数构造函数，该构造函数将结构的字段设置为默认值。在结构上创建无参数构造函数是错误的。

从 C# 10 开始，您可以包含自己的无参数结构构造函数。如果您不提供，则将提供隐式无参数构造函数以将所有字段设置为默认值。您在结构中创建的无参数构造函数必须是公共的并且不能是部分的： 

```C#
public struct Address
{
    public Address()
    {
        City = "<unknown>";
    }
    public string City { get; init; }
}
```

您可以如上所述在无参数构造函数中初始化字段，也可以通过字段或属性初始化程序初始化它们：

```C#
public struct Address
{
    public string City { get; init; } = "<unknown>";
}
```

通过默认创建或作为数组分配的一部分创建的结构会忽略显式无参数构造函数，并始终将结构成员设置为其默认值。有关结构中无参数构造函数的更多信息，请参阅结构类型。

#### 02 Record structs

从 C# 10 开始，现在可以使用 record struct 定义 record。这些类似于 C# 9 中引入的record 类：

```C#
public record struct Person
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

您可以继续使用 record 定义记录类，也可以使用 record 类来清楚地说明。

结构已经具有值相等——当你比较它们时，它是按值。记录结构添加 IEquatable<T> 支持和 == 运算符。记录结构提供 IEquatable<T> 的自定义实现以避免反射的性能问题，并且它们包括记录功能，如 ToString() 覆盖。

记录结构可以是位置的，主构造函数隐式声明公共成员：

```C#
public record struct Person(string FirstName, string LastName);
```

主构造函数的参数成为记录结构的公共自动实现属性。与 record 类不同，隐式创建的属性是读/写的。这使得将元组转换为命名类型变得更加容易。将返回类型从 (string FirstName, string LastName) 之类的元组更改为 Person 的命名类型可以清理您的代码并保证成员名称一致。声明位置记录结构很容易并保持可变语义。

如果您声明一个与主要构造函数参数同名的属性或字段，则不会合成任何自动属性并使用您的。

要创建不可变的记录结构，请将 readonly 添加到结构（就像您可以添加到任何结构一样）或将 readonly 应用于单个属性。对象初始化器是可以设置只读属性的构造阶段的一部分。这只是使用不可变记录结构的一种方法：

```C#
var person = new Person { FirstName = "Mads", LastName = "Torgersen"};

public readonly record struct Person
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

在本文中了解有关记录结构的更多信息。

- [记录结构](https://docs.microsoft.com/dotnet/csharp/language-reference/builtin-types/record)

#### 03 Record类中 ToString () 上的密封修饰符

记录类也得到了改进。从 C# 10 开始，ToString() 方法可以包含 seal 修饰符，这会阻止编译器为任何派生记录合成 ToString 实现。

在本文中的记录中了解有关 ToString () 的更多信息。

- [有关 ToString () 的更多信息](https://docs.microsoft.com/dotnet/csharp/language-reference/builtin-types/record#built-in-formatting-for-display)

#### 04 结构和匿名类型的表达式

C# 10 支持所有结构的 with 表达式，包括记录结构，以及匿名类型：

```C#
var person2 = person with { LastName = "Kristensen" };
```

这将返回一个具有新值的新实例。您可以更新任意数量的值。您未设置的值将保留与初始实例相同的值。

在本文中了解有关 with 的更多信息

- [了解有关 with 的更多信息](https://docs.microsoft.com/dotnet/csharp/language-reference/builtin-types/record#built-in-formatting-for-display) 

### 内插字符串改进

当我们在 C# 中添加内插字符串时，我们总觉得在性能和表现力方面，使用该语法可以做更多事情。

#### 01 内插字符串处理程序

今天，编译器将内插字符串转换为对 string.Format 的调用。这会导致很多分配——参数的装箱、参数数组的分配，当然还有结果字符串本身。此外，它在实际插值的含义上没有任何回旋余地。

在 C# 10 中，我们添加了一个库模式，允许 API “接管”对内插字符串参数表达式的处理。例如，考虑 StringBuilder.Append：

```C#
var sb = new StringBuilder();
sb.Append($"Hello {args[0]}, how are you?");
```

到目前为止，这将使用新分配和计算的字符串调用 Append(string? value) 重载，将其附加到 StringBuilder 的一个块中。但是，Append 现在有一个新的重载 Append(refStringBuilder.AppendInterpolatedStringHandler handler)，当使用内插字符串作为参数时，它优先于字符串重载。
通常，当您看到 SomethingInterpolatedStringHandler 形式的参数类型时，API 作者在幕后做了一些工作，以更恰当地处理插值字符串以满足其目的。在我们的 Append 示例中，字符串 “Hello”、args[0] 和“，how are you？” 将单独附加到 StringBuilder 中，这样效率更高且结果相同。

有时您只想在特定条件下完成构建字符串的工作。一个例子是 Debug.Assert：

```C#
Debug.Assert(condition, $"{SomethingExpensiveHappensHere()}");
```

在大多数情况下，条件为真，第二个参数未使用。但是，每次调用都会计算所有参数，从而不必要地减慢执行速度。Debug.Assert 现在有一个带有自定义插值字符串构建器的重载，它确保第二个参数甚至不被评估，除非条件为假。

最后，这是一个在给定调用中实际更改字符串插值行为的示例：String.Create() 允许您指定 IFormatProvider 用于格式化插值字符串参数本身的洞中的表达式：

```C#
String.Create(CultureInfo.InvariantCulture, $"The result is {result}");
```

您可以在本文和有关创建自定义处理程序的本教程中了解有关内插字符串处理程序的更多信息。

- [创建自定义处理程序](https://docs.microsoft.com/dotnet/csharp/languagereference/tokens/interpolated#compilation-of-interpolated-strings)
- [内插字符串处理程序的更多信息](https://docs.microsoft.com/dotnet/csharp/whats-new/tutorials/interpolated-string-handler)

#### 02 常量内插字符串

如果内插字符串的所有洞都是常量字符串，那么生成的字符串现在也是常量。这使您可以在更多地方使用字符串插值语法，例如属性：

```C#
[Obsolete($"Call {nameof(Discard)} instead")]
```

请注意，必须用常量字符串填充洞。其他类型，如数字或日期值，不能使用，因为它们对文化敏感，并且不能在编译时计算。

## 其他改进

C# 10 对整个语言进行了许多较小的改进。其中一些只是使 C# 以您期望的方式工作。

### 在解构中混合声明和变量

在 C# 10 之前，解构要求所有变量都是新的，或者所有变量都必须事先声明。在 C# 10 中，您可以混合：

```C#
int x2;
int y2;
(x2, y2) = (0, 1);       // Works in C# 9
(var x, var y) = (0, 1); // Works in C# 9
(x2, var y3) = (0, 1);   // Works in C# 10 onwards 
```

在有关解构的文章中了解更多信息。

### 改进的明确分配

如果您使用尚未明确分配的值，C# 会产生错误。C# 10 可以更好地理解您的代码并且产生更少的虚假错误。这些相同的改进还意味着您将看到更少的针对空引用的虚假错误和警告。

在 C# 10 中的新增功能文章中了解有关 C# 确定赋值的更多信息。

- [C# 10 中的新增功能文章](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-10#improved-definite-assignment)

### 扩展的属性模式

C# 10 添加了扩展属性模式，以便更轻松地访问模式中的嵌套属性值。例如，如果我们在上面的 Person 记录中添加一个地址，我们可以通过以下两种方式进行模式匹配：

```C#
object obj = new Person
{
    FirstName = "Kathleen",
    LastName = "Dollard",
    Address = new Address { City = "Seattle" }
};

if (obj is Person { Address: { City: "Seattle" } })
    Console.WriteLine("Seattle");

if (obj is Person { Address.City: "Seattle" }) // Extended property pattern
    Console.WriteLine("Seattle");
```

扩展属性模式简化了代码并使其更易于阅读，尤其是在匹配多个属性时。

在模式匹配文章中了解有关扩展属性模式的更多信息。

- [模式匹配文章](https://docs.microsoft.com/dotnet/csharp/languagereference/operators/patterns#property-pattern)

### 调用者表达式属性

CallerArgumentExpressionAttribute 提供有关方法调用上下文的信息。与其他 CompilerServices 属性一样，此属性应用于可选参数。在这种情况下，一个字符串：

```C#
void CheckExpression(bool condition, 
    [CallerArgumentExpression("condition")] string? message = null )
{
    Console.WriteLine($"Condition: {message}");
}
```

传递给 CallerArgumentExpression 的参数名称是不同参数的名称。作为参数传递给该参数的表达式将包含在字符串中。例如，

```C#
var a = 6;
var b = true;
CheckExpression(true);
CheckExpression(b);
CheckExpression(a > 5);

// Output:
// Condition: true
// Condition: b
// Condition: a > 5
```

ArgumentNullException.ThrowIfNull() 是如何使用此属性的一个很好的示例。它通过默认提供的值来避免必须传入参数名称：

```C#
void MyMethod(object value)
{
    ArgumentNullException.ThrowIfNull(value);
}
```

- [了解有关 CallerArgumentExpressionAttribute 的更多信息](https://docs.microsoft.com/dotnet/csharp/languagereference/attributes/caller-information#argument-expressions)

欢迎在下方留言，告诉我们您的建议或想法，谢谢！

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2022/02/2022-02-12_01.md)