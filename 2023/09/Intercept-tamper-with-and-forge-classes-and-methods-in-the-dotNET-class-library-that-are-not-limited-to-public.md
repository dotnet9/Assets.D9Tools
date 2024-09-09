---
title: 拦截、篡改、伪造.NET类库中不限于public的类和方法
slug: Intercept-tamper-with-and-forge-classes-and-methods-in-the-dotNET-class-library-that-are-not-limited-to-public
description: 本文除了回顾拦截.NET类库中的方法，实现方法参数的篡改、方法返回结果的伪造，再着重介绍.NET类库中非public类及方法如何拦截。
date: 2023-09-22 20:49:57
lastmod: 2023-09-23 12:31:32
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2023/09/cover_07.png
categories: 
    - .NET
tags: 
    - .NET
---

大家好，我是沙漠尽头的狼。

本文首发于[Dotnet9](https://dotnet9.com/2023/09/Intercept-tamper-with-and-forge-classes-and-methods-in-the-dotNET-class-library-that-are-not-limited-to-public)，介绍使用`Lib.Harmony`库拦截第三方`.NET`库方法，达到不修改其源码并能实现修改方法逻辑、预期行为的效果，并且不限于只拦截`public`访问修饰的类及方法，行文目录：

1. 什么是方法拦截？
2. 示例程序拦截
3. 非public方法怎么拦截？
4. 总结

## 1. 什么是方法拦截？

方法拦截是指在方法被调用之前或之后，通过插入自定义的代码来修改方法的行为。通过方法拦截，开发人员可以在不修改原始代码的情况下，对方法的输入参数进行验证、修改方法的返回值、记录方法的调用日志等操作。

本文使用`Lib.Harmony`库实现第三方库方法的拦截，关于该库站长写过[快学会这个技能-.NET API拦截技法]([快学会这个技能-.NET API拦截技法 - Dotnet9](https://dotnet9.com/2023/02/Learn-this-skill-Dotnet-API-interception-technique))一文，大家可以再看看，但该篇文章未介绍非public类及方法如何拦截，本文会有所补充反过来 。

## 2. 示例程序拦截

### 2.1. 编写取数字段落的程序

创建一个`.NET`类库工程，比如叫`TestDll`，添加工具类`TestTool`：

```csharp
namespace TestDll;

public class TestTool
{
    /// <summary>
    /// 带数字的优美段落
    /// </summary>
    private readonly List<string> _sentences = new()
    {
        "一，是孤独的象征，寂寞的代言人， 它独自站在诗句的起点，引人遐想。",
        "二，是相对的存在，对立的伴侣， 它们如影随形，互相依存。",
        "三，是完美的数字，三角的稳定， 它给诗歌带来了和谐的节奏。",
        "四，是平衡的象征，四季的轮回， 它让诗歌的结构更加坚实。",
        "五，是生机勃勃的数字，五彩斑斓的花朵， 它们在诗歌中绽放出美丽的画面。 ",
        "六，是平凡的数字，六边形的形状， 它们给诗歌带来了一种稳定的感觉。",
        "七，是神秘的数字，七色的虹霓， 它们在诗歌中散发出神奇的光芒。",
        "八，是无限的数字，八方的宇宙， 它们让诗歌的想象力无限延伸。",
        "九，是完美的数字，九曲的江河， 它们给诗歌带来了一种流动的美感。 ",
        "十，是圆满的数字，十全十美的象征， 它们让诗歌的结尾更加完美。"
    };

    /// <summary>
    /// 取对应数字的段落
    /// </summary>
    /// <param name="number"></param>
    /// <returns></returns>
    public string GetNumberSentence(int number)
    {
        var mo = number % _sentences.Count;

        // 个位为0，取最后一
        if (mo == 0)
        {
            mo = 10;
        }

        if (mo == 6)
        {
            mo = 1;
        }

        var sentencesIndex = mo - 1;
        return _sentences[sentencesIndex];
    }
}
```

上面的方法`GetNumberSentence`逻辑：传入一个整型`number`参数，除10（集合_sentences项数）取模，返回10以内的数字美文段落，其中如果模为6会取数字1的段落（这是为了验证拦截逻辑设计添加的）。

下面是写的一个`AvaloniaUI`程序测试界面，UI不是本文重点，这里就直接贴动图和代码截图了，文末也有源码链接：

![](https://img1.dotnet9.com/2023/09/0701.gif)

![](https://img1.dotnet9.com/2023/09/0702.png)

### 2.2. 为什么个位数字为6时，总是显示数字1的段落呢？

分析上面的代码，我们想把`mo == 6`时让`mo = 1`逻辑去掉，除了使用`dnSpy`这些反编译工具修改代码，我们还可以使用`Lib.Harmony`（[快学会这个技能-.NET API拦截技法 - Dotnet9](https://dotnet9.com/2023/02/Learn-this-skill-Dotnet-API-interception-technique)）拦截`GetNumberSentence`方法。

1. 安装`Lib.Harmony`包

```xml
<PackageReference Include="Lib.Harmony" Version="2.3.0-prerelease.2" />
```

2. 编写拦截替换类

参考[快学会这个技能-.NET API拦截技法 - Dotnet9](https://dotnet9.com/2023/02/Learn-this-skill-Dotnet-API-interception-technique)添加如下拦截替换类：

- 在拦截类上注册需要拦截的原类类型、原方法名和参数数据类型
- 可以先将原方法内代码复制到拦截替换方法`Prefix`内，对于原类中的属性、字段可通过反射获取(比如`_sentences`集合)
- 将`mo == 6`的代码注释

```csharp
using HarmonyLib;
using System.Reflection;
using TestDll;

namespace MultiVersionLibrary;

/// <summary>
/// HarmonyPatch特性关联拦截的类及方法
/// </summary>
[HarmonyPatch(typeof(TestTool))]
[HarmonyPatch(nameof(TestTool.GetNumberSentence))]
[HarmonyPatch(new Type[] { typeof(int) })]
internal class HookGetNumberSentence
{
    /// <summary>
    /// GetNumberSentence拦截替换方法
    /// </summary>
    /// <param name="__instance">拦截的TestTool实例</param>
    /// <param name="number">GetNumberSentence方法同名参数定义，修改它达到方法参数篡改</param>
    /// <param name="__result">GetNumberSentence方法返回值，修改它达到方法值伪造</param>
    /// <returns></returns>
    public static bool Prefix(ref object __instance, int number, ref string __result)
    {
        try
        {
            //将原方法逻辑全部复制，然后做部分修改

            //1、_sentences是拦截类TestTool的私胡字段，我们通过反射获取值
            var sentences =
                __instance.GetType().GetField("_sentences", BindingFlags.NonPublic | BindingFlags.Instance)
                    ?.GetValue(__instance) as List<string>;
            if (sentences?.Any() != true)
            {
                __result = "啊，没有优美句子吗？";
                return true;
            }

            var mo = number % sentences.Count;

            // 个位为0，取最后一
            if (mo == 0)
            {
                mo = 10;
            }

            // 2、注释我们认为有歧义的代码
            //if (mo == 6)
            //{
            //    mo = 1;
            //}

            var sentencesIndex = mo - 1;
            __result = sentences[sentencesIndex];

            return false;
        }
        catch (Exception ex)
        {
            return true;
        }
    }
}
```

别忘了在`Program`或`App.xaml`初始方法内注册自动拦截类方法：

```csharp
var harmony = new Harmony("https://dotnet9.com");
harmony.PatchAll(Assembly.GetExecutingAssembly());
```

重新运行主程序，输入数字6时正常显示数字6对应的段落了：

![](https://img1.dotnet9.com/2023/09/0703.gif)

这样就达到不修改第三库源码的情况实现结果篡改了，站长使用`.NET 8`拦截会有异常，后改为 `.NET 6` 得以正常运行，异常信息如下，可能是`Lib.Harmony`还不支持`.NET 8`吧：

> HarmonyLib.HarmonyException:“Patching exception in method System.String TestDll.TestTool::GetNumberSentence(System.Int32 number)”
>
> TypeInitializationException: The type initializer for 'MonoMod.Utils.DMDEmitDynamicMethodGenerator' threw an exception.
>
> InvalidOperationException: Cannot find returnType fieeld on DynamicMethod

## 3. 非public方法怎么拦截？

### 3.1. 修改数字段落获取方法

还是修改`TestTool`类，另外增加`GetNumberSentence2`方法，在方法中添加一个数字验证操作`mo = new CalNumber().GetValidNumber(mo);`，方法定义如下：

```csharp
/// <summary>
/// 取对应数字的段落
/// </summary>
/// <param name="number"></param>
/// <returns></returns>
public string GetNumberSentence2(int number)
{
    var mo = number % _sentences.Count;

    // 个位为0，取最后一
    if (mo == 0)
    {
        mo = 10;
    }

    // 新增数字验证方法
    mo = new CalNumber().GetValidNumber(mo);

    var sentencesIndex = mo - 1;
    return _sentences[sentencesIndex];
}
```

验证方法定义如下：

- `CalNumber`类和`GetValidNumber`方法用`internal`声明，意为类或方法只能在当前工程内使用

```csharp
internal class CalNumber
{
    internal int GetValidNumber(int number)
    {
        // 这里可以加一些复杂的算法代码
        if (number == 6)
        {
            number = 1;
        }

        return number;
    }
}
```

并在主工程调用数字获取段落方法处改为：

```csharp
public string? Number
{
    get { return _number; }
    set
    {
        _number = value;
        TryParse(_number, out var factNumber);

        // 换方法2了
        Message = _testTool.GetNumberSentence2(factNumber);
    }
}
```

输入6时又返回1的段落了：

![](https://img1.dotnet9.com/2023/09/0701.gif)

**问题来了：internal方法怎么拦截？**

我们不直接注释代码`mo = new CalNumber().GetValidNumber(mo);`，万一验证方法非常重要，我们只是需要修改其中部分逻辑，总体原逻辑不应该改变。

### 3.2. internal方法怎么拦截？

新增拦截类`HookGetValidNumber`，现在不能再在类上添加特性了（`[HarmonyPatch(typeof(CalNumber))]`），因为`CalNumber`不是public访问修饰，跨工程无法直接使用，语法不支持：

![](https://img1.dotnet9.com/2023/09/0704.png)

特性用不上，那就手工注册需要拦截的方法，这是本文的重点，代码在下面，简单提一下：

- 手工注册代码和自动注册声明特性类似，只是换个写法；
- 拦截替换方法需要使用`HarmonyMethod`方法包装；
- ` harmony.Patch(hookMethod, replaceHarmonyMethod);`用于关联被拦截方法与替换方法

```csharp
/// <summary>
/// 手工注册关联被拦截方法与替换方法
/// </summary>
public static void StartHook()
{
    var harmony = new Harmony("https://dotnet9.com");
    var hookClassType = typeof(TestTool).Assembly.GetType("TestDll.CalNumber");
    var hookMethod = hookClassType!.GetMethod("GetValidNumber", BindingFlags.NonPublic | BindingFlags.Instance,
        new[] { typeof(int) });
    var replaceMethod = typeof(HookGetValidNumber).GetMethod(nameof(Prefix));
    var replaceHarmonyMethod = new HarmonyMethod(replaceMethod);
    harmony.Patch(hookMethod, replaceHarmonyMethod);
}
```

替换方法定义如下：

- `Prefix`方法命名这里不加限制，只要和上面手工注册（`var replaceMethod = typeof(HookGetValidNumber).GetMethod(nameof(Prefix));`）相同即可：
- 数字等于6，修改伪造结果为8

```csharp
/// <summary>
/// GetNumberSentence拦截替换方法
/// </summary>
/// <param name="__instance">拦截的TestTool实例</param>
/// <param name="number">GetNumberSentence方法同名参数定义，修改它达到方法参数篡改</param>
/// <param name="__result">GetNumberSentence方法返回值，修改它达到方法值伪造</param>
/// <returns></returns>
public static bool Prefix(ref object __instance, int number, ref int __result)
{
    //将原方法逻辑全部复制，然后做部分修改

    // 这里可以加一些复杂的算法代码
    if (number == 6)
    {
        number = 8;
    }

    __result = number;

    return false;
}
```

最后在原自动注册代码下，再加一行手工注册代码就OK，打完收工：

```csharp
// 1、自动注册拦截类：拦截类上添加被拦截类和方法特性
var harmony = new Harmony("https://dotnet9.com");
harmony.PatchAll(Assembly.GetExecutingAssembly());

// 2、自动注册拦截类，构造被拦截类和方法信息进行拦截
HookGetValidNumber.StartHook();
```

运行效果如下，输入6显示数字8段落：

![](https://img1.dotnet9.com/2023/09/0705.gif)

## 4. 总结

- 技术交流加群请添加站长微信号：codewf
- 文中示例代码：[MultiVersionLibrary](https://github.com/dotnet9/TerminalMACS.ManagerForWPF/tree/master/src/Demo/MultiVersionLibrary)

使用`Lib.Harmony`库拦截注册有两种方式的用处如下：

1. 自动注册：
   - 通过在拦截类上使用特性关联被拦截类和方法定义，可以实现自动注册拦截逻辑。这种方式适用于需要拦截的类和方法比较多的情况，可以减少手动注册的工作量，提高开发效率。
   - 自动注册通常只能关联public类或方法，因为IDE会根据代码的可见性进行过滤和提示。
2. 手工注册：
   - 通过代码构造被拦截类和方法定义进行手工注册，可以更加灵活地控制拦截逻辑。这种方式适用于需要对拦截逻辑进行定制化处理的情况，可以根据具体需求选择需要拦截的类和方法，并对拦截逻辑进行精细化配置。
   - 手工注册更加灵活，可以拦截包括internal在内的各种类和方法。手工注册可以通过编写代码来实现对非public类和方法的关联，但需要注意的是，这样做可能会增加代码的复杂性和维护成本。
