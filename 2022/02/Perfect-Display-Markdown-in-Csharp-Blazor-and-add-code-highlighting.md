---
title: 完美：C# Blazor中显示Markdown并添加代码高亮
slug: Perfect-Display-Markdown-in-Csharp-Blazor-and-add-code-highlighting
description: 自认为应该是比较完美了，下面说说怎么做的。
date: 2022-02-27 17:46:36
copyright: Original
originalTitle: 完美：C# Blazor中显示Markdown并添加代码高亮
draft: False
cover: https://img1.dotnet9.com/2022/02/cover_14.jpg
categories: 
    - Blazor
tags: 
    - Blazor
    - Markdown
---

昨天发了一篇[介绍这个库：C# Blazor 中显示 Markdown 文件](https://mp.weixin.qq.com/s/CGSJ4qkVdaSg738nSyXhLg)，介绍怎么在 Blazor 中显示 Markdown 内容的文章，文章内的代码是没有高亮的，思来相去，还是要做好，于是百度到这篇文章[.NET C# Blazor 服务端渲染 Markdown](https://blog.csdn.net/qq_37214567/article/details/122949945)，现在渲染效果如下：

![](https://img1.dotnet9.com/2022/02/1601.gif)

自认为应该是比较完美了，下面说说怎么做的。

## 一、准备工具

### 1.1 添加 Markdown 转 html 包：Markdig

[Markdig](https://github.com/xoofx/markdig)：Markdig 是一个快速、强大、符合[CommonMark](http://commonmark.org/)标准、可扩展的 .NET Markdown 处理器。

```xml
<PackageReference Include="Markdig" Version="0.27.0" />
```

## 1.2 引入 Prism 插件

此[Prism](https://github.com/PrismJS/prism)非彼[Prism](https://github.com/PrismLibrary/Prism)，是一个 JS 插件：Prism 是一个轻量级、健壮且优雅的语法高亮库。这是[Dabblet](https://dabblet.com/)的一个衍生项目。

在`_Layout.cshtml`的`head`中引入：

```html
<head>
  ....
  <!--重置浏览器样式-->
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/normalize.css@8.0.1/normalize.css"
  />
  <!--代码块主题-->
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/prismjs@1.27.0/themes/prism-coy.min.css"
  />
  <!--工具栏插件-->
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/prismjs@1.27.0/plugins/toolbar/prism-toolbar.min.css"
  />
  <!--行号插件-->
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/prismjs@1.27.0/plugins/line-numbers/prism-line-numbers.min.css"
  />
  ...
</head>
<body>
  ...
  <!--prism核心js (用于渲染代码块)-->
  <script src="https://cdn.jsdelivr.net/npm/prismjs@1.27.0/prism.min.js"></script>
  <!--显示代码块行号-->
  <script src="https://cdn.jsdelivr.net/npm/prismjs@1.27.0/plugins/line-numbers/prism-line-numbers.min.js"></script>
  <!--工具栏(一些插件的前置依赖)-->
  <script src="https://cdn.jsdelivr.net/npm/prismjs@1.27.0/plugins/toolbar/prism-toolbar.min.js"></script>
  <!--代码块显示语言名称-->
  <script src="https://cdn.jsdelivr.net/npm/prismjs@1.27.0/plugins/show-language/prism-show-language.min.js"></script>
  <!--复制代码-->
  <script src="https://cdn.jsdelivr.net/npm/prismjs@1.27.0/plugins/copy-to-clipboard/prism-copy-to-clipboard.min.js"></script>
  <!--自动去cdn加载对应语言的代码高亮js-->
  <script src="https://cdn.jsdelivr.net/npm/prismjs@1.27.0/plugins/autoloader/prism-autoloader.min.js"></script>
</body>
```

## 二、使用

我将`Markdown`展示单独提取成了组件`MarkdownComponent.razor`，将加载的`Markdown`文件相对路径、需要链接的文章链接和源码链接做成参数，方便后面其他工具复用，下面的代码片段主要在这个文件内。

组件参数定义：

```C#
@code {
    [Parameter]
    public string LocalPostFilePath { get; set; } = null!;

    [Parameter]
    public string RemotePostUrl { get; set; } = null!;

    [Parameter]
    public string SourceCodeUrl { get; set; } = null!;
}
```

`Markdown`内容读取，`Markdown`格式转`html`在`OnInitializedAsync()`方法中定义：

```C#
protected override async Task OnInitializedAsync()
{
    var markdownData = await File.ReadAllTextAsync(LocalPostFilePath);

    // markdown 转为 html
    var htmlData = Markdown.ToHtml(markdownData);

    // 转为 prism 支持的语言标记（不是必须，可以删除）
    htmlData = htmlData.Replace("language-golang", "language-go");

    // TODO: 使用 https://github.com/mganss/HtmlSanitizer 清洗html中的xss
    if (htmlData.Contains("<script") || htmlData.Contains("<link"))
    {
        _hasXss = true;
    }

    // 将 普通文本 转为 可以渲染的html的类型
    _postHtmlContent = (MarkupString) htmlData;
}
```

最后一步，需要在组件完成后，调用`Prism`插件方法，写在方法`OnAfterRenderAsync(bool firstRender)`中，这是做代码高亮的关键代码：

```C#
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    await _jsRuntime.InvokeVoidAsync("Prism.highlightAll");
}
```

渲染相对来说就简单了（只针对我们使用），见下面的代码：

```html
<div class="line-numbers">
  @{ if (_hasXss) { @_postHtmlContent.ToString() } else { @_postHtmlContent } }
</div>
```

在`IcoTool.razor`调用该组件：

```html
<MarkdownComponent
  LocalPostFilePath="wwwroot/2022/02/2022-02-22_02.md"
  RemotePostUrl="https://dotnet9.com/2022/02/Perfect-Display-Markdown-in-Csharp-Blazor-and-add-code-highlighting"
  SourceCodeUrl="https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools.Web/Pages/Public/ImageTools/IcoTool.razor"
/>
```

当然组件封装看个人需求，大致思路是上面的，就不贴详细代码了，有兴趣看看[Dotnet9 工具箱源码](https://github.com/dotnet9/dotnet9.com)。

参考文章：

- [.NET C# Blazor 服务端渲染 Markdown](https://blog.csdn.net/qq_37214567/article/details/122949945)
