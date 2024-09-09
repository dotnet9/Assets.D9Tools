---
title: 介绍这个库：C# Blazor中显示Markdown文件
slug: Introducing-this-library-Displaying-Markdown-files-in-csharp-Blazor
description: 我的想法是，除了提供工具免费使用外，也能让大家了解这个工具是如果开发的，这样应该更方便
date: 2022-02-26 22:15:26
copyright: Original
originalTitle: 介绍这个库：C# Blazor中显示Markdown文件
draft: False
cover: https://img1.dotnet9.com/2022/02/cover_14.jpg
categories: .NET
tags: 
    - Blazor
    - Markdown
---

## 1 讲目的

前几天上线了一个[在线 Icon 转换工具](https://tool.dotnet9.com/ico)，为了让大家使用放心，改了点代码，在转换下载 Icon 图标后立即删除临时文件，并在工具下面贴上了工具的开发步骤和代码，大家看这样改是否合适，见[Issue 1](https://github.com/dotnet9/dotnet9.com/issues/1)。

这篇不讲代码修改过程(因为[工具](https://tool.dotnet9.com/ico)和网站[博文](https://dotnet9.com/1715)已经同步更新)，本文讲讲在工具下方展示 Markdown 文件的实现方式，先看效果：

![Blazor中显示Markdown](https://img1.dotnet9.com/2022/02/1401.gif)

**为啥要加这个功能？**

我的想法是，除了提供工具免费使用外，也能让大家了解这个工具是如果开发的，这样应该更方便：

1. 默认是不显示的，点击`如何开发的？`的按钮加载开发文章说明。
2. 评论功能目前没有（不排除后面加上），需要点击`我要建议（吐槽）`按钮跳转到[Dotnet9](https://dotnet9.com)网站同篇[博文](https://dotnet9.com/2022/02/Introducing-this-library-Displaying-Markdown-files-in-csharp-Blazor)留言。
3. 旁边有个按钮`我要浏览源码`可以点击浏览工具源码。

下面说说在 Blazor 中怎么展示 Markdown 文件，先说明目前完成的功能：

1. 只是将 Markdown 文件展示为 html。
2. 高亮目前未加。

## 2 开发步骤

参考[blazor-markdown](https://github.com/georgemathieson/blazor-markdown)。

1. 引入包

```XML
<PackageReference Include="BlazorMarkdown" Version="1.0.0" />
<PackageReference Include="HtmlSanitizer" Version="7.1.488" />
```

2. 注入组件

`Program.cs`

```C#
builder.Services.AddScoped<IHtmlSanitizer, HtmlSanitizer>(x =>
{
    // Configure sanitizer rules as needed here.
    // For now, just use default rules + allow class attributes
    var sanitizer = new HtmlSanitizer();
    sanitizer.AllowedAttributes.Add("class");
    return sanitizer;
});
```

3. 引用命名空间

`_Imports.razor`

```html
@using BlazorMarkdown
```

4. 使用

准备好 Markdown 文件，比如我放 wwwroot 下：

![Markdown文件](https://img1.dotnet9.com/2022/02/1402.png)

在[IcoTool.razor](https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools.Web/Pages/Public/ImageTools/IcoTool.razor)中就可以直接使用了：

```html
<Markdown FilePath="wwwroot/2022/02/2022-02-22_02.md" />
```

## 总结

完了，就是这么简单，效果见文章开头，今天不啰嗦。

忘了，markdown 中有图片等多媒体文件，记得加上这些样式实现自适应：

```css
<style>
    h3 {
        border-bottom: 1px solid #eee;
        margin-top: 50px;
        padding-bottom: 10px;
    }

    pre {
        background: #1E1E1E;
        color: #eee;
        overflow-x: auto;
        padding: 0.5em !important;
        white-space: pre;
        word-break: normal;
        word-wrap: normal;
    }

    img, video, source { max-width: 100% }

    pre > code { white-space: pre; }
</style>
```
