---
title: (6/30)大家一起学Blazor：抽离C#代码
slug: 6-of-30-let-us-learn-blazor-together-extract-csharp-code
description: 昨天看到`FetchData.razor`的代码太长了，为求方便，我们把`@code`的部分抽取出来另成一个文件。
date: 2021-12-12 21:59:24
copyright: Reprinted
author: StrayaWorker
originalTitle: (6/30)大家一起学Blazor：抽离C#代码
originalLink: https://ithelp.ithome.com.tw/articles/10260867
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
categories: .NET
tags: 
    - Blazor
    - ASP.NET Core
    - 学Blazor
---

昨天看到`FetchData.razor`的代码太长了，为求方便，我们把`@code`的部分抽取出来另成一个文件。

Blazor 提供了两个方法：`partial class`跟`ComponentBase`，同样两者各有优缺点，笔者偏好`ComponentBase`，看个人使用习惯。

先来看`partial class`，我们打开`FetchData.razor`，把前面提到的`@using BlazorServer.Data`移到`_Imports.razor`，再建立一个`class`叫做`FetchData.razor.cs`，在`public`跟`class`中间加上`partial`修饰词，接着把`FetchData.razor`的`@code`区块剪切贴到`FetchData.razor.cs`后稍作修改，可以看出跟旧的 C#代码没什么差别。我们按下 F5，一样看到 10 条天气数据。

原 FetchData.razor：

![原FetchData.razor](https://img1.dotnet9.com/2021/12/1201.png)

提取 C#代码后代码截图：

![提取C#代码后代码截图](https://img1.dotnet9.com/2021/12/1202.png)

页面展示不变：

![页面展示不变](https://img1.dotnet9.com/2021/12/1203.png)

接着来看`ComponentBase`，先把`partial class`里面的代码复制下来，接着建立一个新 class 取名为`FetchDataBase.cs`，将刚刚的代码贴上后稍做修改，再删除`FetchData.razor.cs`这个 partial class，因为两种模式不能并存。可以看到除了继承`ComponentBase`跟加上`[Inject]`外，几乎没有差别，这里的`[Inject]`相当于在`FetchData.razor.cs`使用`@inject WeatherForecastService ForecastService`，我们按下 F5 启动调试模式，在 14 行左边按一下点击中断点，可以看到一样取得了天气数据。

使用 ComponentBase：

![使用ComponentBase](https://img1.dotnet9.com/2021/12/1204.png)

接着开始做自己的 Component 吧！先将不必要的 Component 删除，Program.cs 和\_Import.razor 里面跟天气有关的 using 及注册同样删除，如果忘记这两个文件在哪里，可以在删除 Component 后将滑鼠移到项目 BlazorServer 点右键，选取重建项目，Visual Studio 就会告诉你哪里出错了。

删除多余组件等文件：

![删除多余组件等文件](https://img1.dotnet9.com/2021/12/1205.png)

之前说过要完成让使用者写日志的网站，所以需要最基本的输入框，而日志的单位就以一篇计算。首先建立 Models 文件夹，建立`PostModel`类型，里面很简单只有 3 个属性，接着在 Pages 文件夹建立`Post.razor`跟`PostBase.razor.cs`，最后将`NavMenu.razor`的连接留下一个，`href`属性的值改为`Post`。

/Models/PostModel.cs

```C#
namespace BlazorServer.Models;

public class PostModel
{
	public int Id { get; set; }

	public string? Title { get; set; }

	public string? Content { get; set; }
}
```

/Shared/NavMenu.razor：

```html
<div class="@NavMenuCssClass" @onclick="ToggleNavMenu">
  <nav class="flex-column">
    <div class="nav-item px-3">
      <NavLink class="nav-link" href="/Post" Match="NavLinkMatch.All">
        <span class="oi oi-home" aria-hidden="true"></span> Post
      </NavLink>
    </div>
  </nav>
</div>
```

`PostModel`是用来承接数据的容器，目前没有 Service，所以在`PostBase.razor.cs`放一笔假数据，这边可以看到一个方法`OnInitializedAsync()`，代表当这个 Component 生命周期开始，里面的事情就会先做，其他还有`OnAfterRenderAsync`、`OnParametersSetAsync`等等，只要先输入 override 再按一下空白键，就可以看到这些方法，且也有同步跟异步两种模式，这些方法有机会再说明。

![PostBase](https://img1.dotnet9.com/2021/12/1206.png)

而`Post.razor`用了`EditForm`这个 Component，编译过后相当于 html 的 form 元素，里面还有 3 个 input 元素，Blazor 也有提供相对应的 Input Component，可以从官方文件看到分别编译后的 html 元素。

```html
@page "/Post" @inherits PostBase

<EditForm Model="@Post">
  <input type="number" value="@Post!.Id" />
  <input type="text" value="@Post!.Title" />
  <textarea value="@Post!.Content"></textarea>

  <button type="submit">Submit</button>
</EditForm>
```

上下两种写法对比：

```html
@page "/Post" @inherits PostBase

<EditForm Model="@Post">
  <InputNumber @bind-Value="Post!.Id"></InputNumber>
  <InputText @bind-Value="Post!.Title"></InputText>
  <InputTextArea @bind-Value="Post!.Content"></InputTextArea>

  <button type="submit">Submit</button>
</EditForm>
```

Blazor 提供相对应的 Input Component：

![Input Component](https://img1.dotnet9.com/2021/12/1207.png)

这时候打开网页来看，可以看到上面出现了我们定义在`PostBase.razor.cs`的值，但这是怎么连接起来的呢？原因就是 EditForm 的 Model 属性及 3 个`<Input>`Component 的属性`@bind-Value`，这里在告诉 Blazor：我的 Model 跟里面的值要跟这个 EditForm 绑在一起，如果这里有跟后端代码连接，网页上输入的内容经过事件触发后，就会提交后端处理。

![Post页面展示](https://img1.dotnet9.com/2021/12/1208.png)

不过`<Input>`预设的 CSS 样式不太好看，我们先套用基本的 boostrap 样式；因为 Id 通常不会让使用者输入，所以这边先注释，然后再加入表格验证机制，毕竟不能让使用者随便输入就提交表格，但如果不想自己写一堆验证机制呢？可以试试看 Blazor 的`DataAnnotationsValidator`及`ValidationSummary`两个 Component。

![添加样式](https://img1.dotnet9.com/2021/12/1209.png)

我们先在 PostModel 的 Title 跟 Content 加上两个 Attribute，Required 代表必填，MaxLength 及 MinLength 则是限制最大及最小字数，还可以自定义错误信息。接着在 EditForm 里面加上那两个 ComponentDataAnnotationsValidator 及 ValidationSummary，第一个是验证各个 Input，第二个则是将错误信息显示在表格上方。

![添加验证](https://img1.dotnet9.com/2021/12/1210.png)

![验证错误提示](https://img1.dotnet9.com/2021/12/1211.png)

但如果不喜欢验证机制预设的 CSS 样式呢？Blazor 也提供了定制化的方法。先建立一个`class`名为`CustomFieldClassProvider`且继承`FieldCssClassProvider`，重载方法`GetFieldCssClass`，里面的内容待会再说。

```C#
using Microsoft.AspNetCore.Components.Forms;

namespace BlazorServer;

public class CustomFieldClassProvider : FieldCssClassProvider
{
	public override string GetFieldCssClass(EditContext editContext, in FieldIdentifier fieldIdentifier)
	{
		var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

		return isValid ? "text-primary" : "text-danger";
	}
}
```

接着在`PostBase.razor.cs`加入一个类型为`EditContext`的字段`EditContext`，初始化字段`EditContext`，将原本的`Post`字段丢进去，然后调用`EditContext`的方法`SetFieldCssClassProvider`，添加`CustomFieldClassProvider`实例。

![自定义样式关联](https://img1.dotnet9.com/2021/12/1212.png)

最后最重要的一步，在`Post.razor`将`EditForm`的`Model`参数移除，改为`EditContext`参数，里面的值就是刚刚的`EditContext`字段。

![替换Model为EditContext](https://img1.dotnet9.com/2021/12/1213.png)

这时候再提交表单一次，可以看到 textarea 的红色外框消失了，字体也变成`text-danger`的红色，而正确的字段值则是变成`text-primary`的蓝色。我们再回头看`CustomFieldClassProvider`，原来`EditContext`指的就是`EditForm`的内容，`fieldIdentifier`则是当前验证的`Input`标签，如果`EditContext`调用的方法`GetValidationMessages`有在`fieldIdentifier`取得任何信息，代表这是错误的字段值，没有则是正确字段值，这就是 Blazor 帮我们定制化 Form 的作法。

![定制的数据验证提示](https://img1.dotnet9.com/2021/12/1214.png)

**引用:**

1. [Split HTML And C# Code In Blazor Using Either Partial Class Or ComponentBase Class](https://www.learmoreseekmore.com/2020/06/blazor-paratial-class-or-componentbase-class.html)
2. [ASP.NET Core Blazor forms and validation](https://docs.microsoft.com/en-us/aspnet/core/blazor/forms-validation?view=aspnetcore-5.0#binding-a-form)
3. [Custom validation CSS class attributes](https://docs.microsoft.com/en-us/aspnet/core/blazor/forms-validation?view=aspnetcore-5.0#custom-validation-css-class-attributes-1)

**注：本文代码通过 .NET 6 + Visual Studio 2022 重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**

- 本文 Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/12/2021-12-12_02.md)
