---
title: 这是Blazor上传文件的最佳方式吗？
slug: Is-this-the-best-way-for-blazor-to-upload-files
description: Blazor不得不说真是好东西，极大的提升了开发效率，很多的页面交互功能基本上只需要写很少的代码就能实现了，而且还是无js实现，你也绝对没有想到过，Blazor实现文件上传是有多么简单！
date: 2022-03-16 07:36:14
copyright: Reprinted
author: 懒得勤快
originalTitle: 这是Blazor上传文件的最佳方式吗？
originalLink: https://masuit.org/1676
draft: False
cover: https://img1.dotnet9.com/2022/03/cover_09.gif
categories: 
    - .NET
tags: 
    - Blazor Server
    - 上传文件
---

Blazor 不得不说真是好东西，极大的提升了开发效率，很多的页面交互功能基本上只需要写很少的代码就能实现了，而且还是无 js 实现，你也绝对没有想到过，Blazor 实现文件上传是有多么简单！

先说结论：Blazor 实现带进度显示的文件上传真的很简单！效果看图：

![](https://img1.dotnet9.com/2022/03/cover_09.gif)

实现这么一个小功能，仅仅只花了不到 50 行的代码就实现了，接下来就给大家分享下案例实现吧。

首先引入`Tewr.Blazor.FileReader`包，这个包能够提供文件上传的流式读取，这样便可以实现在服务端对上传文件进行一边上传一遍写文件的操作。

配置依赖注入(站长注：这是 Blazor Server 模式，wasm 方式请查看文末仓库文档说明)：

```C#
services.AddFileReaderService();
```

接下来我们先进行页面布局，很简单，再声明两个变量用于显示进度和显示图片：

```html
<input type="file" /><button>上传文件</button>
<div>
  @if (!string.IsNullOrEmpty(_src)) {
  <img src="@_src" width="600px" />
  } else {
  <p>@progress</p>
  }
</div>
```

然后在组件中注入 IFileReaderService 服务

```html
@using Tewr.Blazor.FileReader @inject IFileReaderService fileReaderService;
```

为了让文件框能够和 C#代码进行交互，所以需要将它通过 ElementReference 引用起来:

```html
<input @ref="inputTypeFileElement" type="file" /><button>上传文件</button>
<div>
  @if (!string.IsNullOrEmpty(_src)) {
  <img src="@_src" width="600px" />
  } else {
  <p>@progress</p>
  }
</div>
@code { private ElementReference inputTypeFileElement; private string _src;
private string progress; }
```

给按钮绑定事件，按钮触发后通过 fileReaderService 进行文件流的读取，接下来便是常规的二进制数据 copy 操作，可以拿到文件的传输进度，计算之后便能显示到页面中

```html
<button @onclick="ReadFile">上传文件</button>
```

```C#
public async Task ReadFile()
{
    _src = "";
    foreach (var file in await fileReaderService.CreateReference(inputTypeFileElement).EnumerateFilesAsync())
    {
        await using var fileStream = await file.OpenReadAsync();
        var buffer = new byte[2048];
        var finalBuffer = new byte[fileStream.Length];
        int count;
        int totalCount = 0;
        while ((count = await fileStream.ReadAsync(buffer, 0, buffer.Length)) != 0)
        {
            Buffer.BlockCopy(buffer, 0, finalBuffer, totalCount, count);
            totalCount += count;
            progress = "文件上传中 " + (int)(totalCount * 100.0 / fileStream.Length) + "%";
            StateHasChanged();
        }
        _src = $"data:image/jpg;base64,{Convert.ToBase64String(finalBuffer)}";
        progress = "";
        StateHasChanged();
    }
}
```

完整代码如下：

```html
@page "/counter" @using Tewr.Blazor.FileReader @inject IFileReaderService
fileReaderService;

<input @ref="inputTypeFileElement" type="file" />
<button @onclick="ReadFile">上传文件</button>
<div>
  @if (!string.IsNullOrEmpty(_src)) {
  <img src="@_src" width="600px" />
  } else {
  <p>@progress</p>
  }
</div>

@code { private ElementReference inputTypeFileElement; private string _src;
private string progress; public async Task ReadFile() { _src = ""; foreach (var
file in await
fileReaderService.CreateReference(inputTypeFileElement).EnumerateFilesAsync()) {
await using var fileStream = await file.OpenReadAsync(); var buffer = new
byte[2048]; var finalBuffer = new byte[fileStream.Length]; int count; int
totalCount = 0; while ((count = await fileStream.ReadAsync(buffer, 0,
buffer.Length)) != 0) { Buffer.BlockCopy(buffer, 0, finalBuffer, totalCount,
count); totalCount += count; progress = "文件上传中 " + (int)(totalCount * 100.0
/ fileStream.Length) + "%"; StateHasChanged(); } _src =
$"data:image/jpg;base64,{Convert.ToBase64String(finalBuffer)}"; progress = "";
StateHasChanged(); } } }
```

**站长插播：**

文章首图演示的是一张不到`1MB`的图片，因为`Tewr.Blazor.FileReader`这个包提供文件上传的流式读取，上传大文件也是可以的，下面这是上传一个`34.2MB`的 ZIP 压缩包，Blazor 服务端模式：

![](https://img1.dotnet9.com/2022/03/0901.gif)

demo 做的一般，可能 gif 看不出啥，只是为了证明这个包确实不错，要实现大文件上传，可把上面单包读取大小改大一点，比如：512KB:

```C#
var buffer = new byte[1024*512];
```

如果看下方微软 Blazor 文件上传文档，把单包大小改成大于 20KB，页面可能会卡一下，然后页面自动刷新就把上传操作给重置了，而使用这个包确没这个问题，这个包很 nice。

OK，本文完

**参考**

- [Blazor 实现文件上传带进度显示案例分享](https://masuit.com/1676)
- [https://github.com/Tewr/BlazorFileReader](https://github.com/Tewr/BlazorFileReader)
- [https://docs.microsoft.com/zh-cn/aspnet/core/blazor/file-uploads?view=aspnetcore-6.0&pivots=server](https://docs.microsoft.com/zh-cn/aspnet/core/blazor/file-uploads?view=aspnetcore-6.0&pivots=server)
