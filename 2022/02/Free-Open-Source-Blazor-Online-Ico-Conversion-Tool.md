---
title: 免费开源Blazor在线Ico转换工具
slug: Free-Open-Source-Blazor-Online-Ico-Conversion-Tool
description: 免费开源Blazor在线Ico转换工具，不保存源文件及转换后文件，下载完成即删除，请放心使用。
date: 2022-02-22 21:38:26
copyright: Original
originalTitle: 免费开源Blazor在线Ico转换工具
draft: False
cover: https://img1.dotnet9.com/2022/02/cover_13.jpg
categories: .NET
tags: .NET 7,Blazor,Ico图标转换,在线工具,免费,开源
---

> 免费开源 Blazor 在线 Ico 转换工具，不保存源文件及转换后文件，下载完成即删除，请放心使用。

**行文目录**

- 1. 功能演示
- 2. 实现说明
  - 2.1 其他图片上传
  - 2.2 核心代码：其他图片转 Ico
  - 2.3 转换后的 Ico 文件下载
- 3. 总结

## 1. 功能演示

仓库地址：[IcoTool](https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools.Web/Pages/Public/ImageTools/IcoTool.razor)

在线演示地址：[https://tool.dotnet9.com/ico](https://tool.dotnet9.com/ico)

演示下文件上传、转换结果：

![](https://img1.dotnet9.com/2022/02/1301.gif)

通过该工具及代码，能了解到：

1. 使用 Blazor 怎么上传文件到服务器(Blazor Server)。
2. 怎么从服务器下载文件。
3. 如何将 png 等图片转换为 Ico 图片。

下面对该工具的实现代码做个简单说明，不太清楚的可以留言交流。

## 2. 实现说明

### 2.1 其他图片上传

使用的[MASA Blazor](https://masa-blazor-docs-dev.lonsid.cn/)上传组件[MFileInput](https://masa-blazor-docs-dev.lonsid.cn/components/file-inputs)，看下面的代码，就一个上传组件加上传时文件保存操作，代码文件：[IcoTool.razor](https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools.Web/Pages/Public/ImageTools/IcoTool.razor)

```html
<MFileInput TValue="IBrowserFile"
            Placeholder="@T("IcoToolMFileInputPlaceholder")"
            Rules="_rules"
            ShowSize
            OnChange="@LoadFile"
            Accept="image/png, image/jpeg, image/jpg, image/bmp"
            Label="@T("IcoToolMFileInputLabel")">
</MFileInput>

@code {
    private readonly List<Func<IBrowserFile, StringBoolean>> _rules = new();

    private bool _loading;
    private IBrowserFile? _sourceBrowserFile;

    [Inject]
    public I18n I18N { get; set; } = default!;

    [Inject]
    public IJSRuntime Js { get; set; } = default!;

    protected override async Task OnInitializedAsync()
    {
        _rules.Add(value => value == null || value.Size < 2 * 1024 * 1024 ? true : T("IcoToolFileSizeLimitMessage"));
        await base.OnInitializedAsync();
    }

    private void LoadFile(IBrowserFile? e)
    {
        _sourceBrowserFile = e;
    }
}
```

上面的代码，看`LoadFile(IBrowserFile? e)`方法，选择文件时只保存`IBrowserFile`引用，选择了后即显示`立即转换并下载Icon`按钮，代码如下：

```html
@if (_sourceBrowserFile != null) {
<MButton
  class="ma-2 white--text"
  Loading="_loading"
  Disabled="_loading"
  Depressed
  Color="primary"
  OnClick="@ConvertAndDownloadIcon"
>
  <LoaderContent>
    <span>@T("IcoToolMButtonLoaderContent")</span>
  </LoaderContent>
  <ChildContent>
    <span>@T("IcoToolMButtonChildContent")</span>
  </ChildContent>
</MButton>
}
```

此时，源文件并未做上传操作，用户还可以重新选择，点击`立即转换并下载Icon`按钮才会执行图标转换和下载操作，下面会说。

### 2.2 核心代码：其他图片转 Ico

参考代码：[https://gist.github.com/darkfall/1656050](https://gist.github.com/darkfall/1656050)

因为使用到`Bitmap`，vs 会提示只支持`Windows`平台（注意重点：**只是该包`System.Drawing.Common`不支持跨平台，不是.NET 6 不支持跨平台，.NET 6 支持跨平台**。），目前工具程序也部署在`Windows Server 2019`服务器上，如果有其他转换代码，支持跨平台欢迎技术讨论，下面给出我使用的其他图片转 Ico 的代码，代码路径在：[ImagingHelper.cs](https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools/Images/ImagingHelper.cs)

```C#
using System.Drawing;
using System.Drawing.Drawing2D;
using System.Drawing.Imaging;

namespace Dotnet9.Tools.Images;

/// <summary>
///     Adapted from this gist: https://gist.github.com/darkfall/1656050
///     Provides helper methods for imaging
/// </summary>
public static class ImagingHelper
{
    public const string FileheadBmp = "6677";
    public const string FileheadJpg = "255216";
    public const string FileheadPng = "13780";
    public const string FileheadGif = "7173";

    private static readonly Dictionary<ImageType, string> ImageTypeHead = new()
    {
        { ImageType.Bmp, FileheadBmp },
        { ImageType.Jpg, FileheadJpg },
        { ImageType.Png, FileheadPng },
        { ImageType.Gif, FileheadGif }
    };

    public static bool IsPicture(string filePath, out string fileHead)
    {
        fileHead = string.Empty;

        try
        {
            var fs = new FileStream(filePath, FileMode.Open, FileAccess.Read);
            var reader = new BinaryReader(fs);
            var fileClass = $"{reader.ReadByte().ToString()}{reader.ReadByte().ToString()}";
            reader.Close();
            fs.Close();
            if (fileClass is not (FileheadBmp or FileheadJpg or FileheadPng or FileheadGif))
                return false;

            fileHead = fileClass;
            return true;
        }
        catch
        {
            return false;
        }
    }

    public static bool IsPictureType(string filePath, ImageType imageType)
    {
        var isPicture = IsPicture(filePath, out var fileHead);
        if (!isPicture) return false;

        return ImageTypeHead[imageType] == fileHead;
    }

    /// <summary>
    ///     Converts a PNG image to a icon (ico) with all the sizes windows likes
    /// </summary>
    /// <param name="inputBitmap">The input bitmap</param>
    /// <param name="output">The output stream</param>
    /// <returns>Wether or not the icon was succesfully generated</returns>
    public static bool ConvertToIcon(Bitmap inputBitmap, Stream output)
    {
        var sizes = new[] { 256, 48, 32, 16 };

        // Generate bitmaps for all the sizes and toss them in streams
        var imageStreams = new List<MemoryStream>();
        foreach (var size in sizes)
        {
            var newBitmap = ResizeImage(inputBitmap, size, size);

            var memoryStream = new MemoryStream();
            newBitmap.Save(memoryStream, ImageFormat.Png);
            imageStreams.Add(memoryStream);
        }

        var iconWriter = new BinaryWriter(output);

        var offset = 0;

        // 0-1 reserved, 0
        iconWriter.Write((byte)0);
        iconWriter.Write((byte)0);

        // 2-3 image type, 1 = icon, 2 = cursor
        iconWriter.Write((short)1);

        // 4-5 number of images
        iconWriter.Write((short)sizes.Length);

        offset += 6 + 16 * sizes.Length;

        for (var i = 0; i < sizes.Length; i++)
        {
            // image entry 1
            // 0 image width
            iconWriter.Write((byte)sizes[i]);
            // 1 image height
            iconWriter.Write((byte)sizes[i]);

            // 2 number of colors
            iconWriter.Write((byte)0);

            // 3 reserved
            iconWriter.Write((byte)0);

            // 4-5 color planes
            iconWriter.Write((short)0);

            // 6-7 bits per pixel
            iconWriter.Write((short)32);

            // 8-11 size of image data
            iconWriter.Write((int)imageStreams[i].Length);

            // 12-15 offset of image data
            iconWriter.Write(offset);

            offset += (int)imageStreams[i].Length;
        }

        for (var i = 0; i < sizes.Length; i++)
        {
            // write image data
            // png data must contain the whole png data file
            iconWriter.Write(imageStreams[i].ToArray());
            imageStreams[i].Close();
        }

        iconWriter.Flush();

        return true;
    }

    /// <summary>
    ///     Converts a PNG image to a icon (ico)
    /// </summary>
    /// <param name="input">The input stream</param>
    /// <param name="output">The output stream</param
    /// <returns>Wether or not the icon was succesfully generated</returns>
    public static bool ConvertToIcon(Stream input, Stream output)
    {
        var inputBitmap = (Bitmap)Image.FromStream(input);
        return ConvertToIcon(inputBitmap, output);
    }

    /// <summary>
    ///     Converts a PNG image to a icon (ico)
    /// </summary>
    /// <param name="inputPath">The input path</param>
    /// <param name="outputPath">The output path</param>
    /// <returns>Wether or not the icon was succesfully generated</returns>
    public static bool ConvertToIcon(string inputPath, string outputPath)
    {
        using var inputStream = new FileStream(inputPath, FileMode.Open);
        using var outputStream = new FileStream(outputPath, FileMode.OpenOrCreate);
        return ConvertToIcon(inputStream, outputStream);
    }


    /// <summary>
    ///     Converts an image to a icon (ico)
    /// </summary>
    /// <param name="inputImage">The input image</param>
    /// <param name="outputPath">The output path</param>
    /// <returns>Wether or not the icon was succesfully generated</returns>
    public static bool ConvertToIcon(Image inputImage, string outputPath)
    {
        using var outputStream = new FileStream(outputPath, FileMode.OpenOrCreate);
        return ConvertToIcon(new Bitmap(inputImage), outputStream);
    }


    /// <summary>
    ///     Resize the image to the specified width and height.
    ///     Found on stackoverflow: https://stackoverflow.com/questions/1922040/resize-an-image-c-sharp
    /// </summary>
    /// <param name="image">The image to resize.</param>
    /// <param name="width">The width to resize to.</param>
    /// <param name="height">The height to resize to.</param>
    /// <returns>The resized image.</returns>
    public static Bitmap ResizeImage(Image image, int width, int height)
    {
        var destRect = new Rectangle(0, 0, width, height);
        var destImage = new Bitmap(width, height);

        destImage.SetResolution(image.HorizontalResolution, image.VerticalResolution);

        using var graphics = Graphics.FromImage(destImage);
        graphics.CompositingMode = CompositingMode.SourceCopy;
        graphics.CompositingQuality = CompositingQuality.HighQuality;
        graphics.InterpolationMode = InterpolationMode.HighQualityBicubic;
        graphics.SmoothingMode = SmoothingMode.HighQuality;
        graphics.PixelOffsetMode = PixelOffsetMode.HighQuality;

        using var wrapMode = new ImageAttributes();
        wrapMode.SetWrapMode(WrapMode.TileFlipXY);
        graphics.DrawImage(image, destRect, 0, 0, image.Width, image.Height, GraphicsUnit.Pixel, wrapMode);

        return destImage;
    }
}

public enum ImageType
{
    Bmp,
    Jpg,
    Png,
    Gif
}
```

简单的单元测试还是要有的，代码见：[ImageHelperTests.cs](https://github.com/dotnet9/dotnet9.com/blob/develop/src/test/Dotnet9.Tools.Tests/Images/ImageHelperTests.cs)

```C#
using Dotnet9.Tools.Images;

namespace Dotnet9.Tools.Tests.Images;

public class ImageHelperTests
{
    [Fact]
    public void IsPicture()
    {
        var testFilePath = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "TestFiles", "logo.png");
        Assert.True(File.Exists(testFilePath));

        var isPicture = ImagingHelper.IsPicture(testFilePath, out var typename);

        Assert.True(isPicture);
    }

    [Fact]
    public void IsNotPicture()
    {
        var testFilePath = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "TestFiles", "test.txt");
        Assert.True(File.Exists(testFilePath));

        var isPicture = ImagingHelper.IsPicture(testFilePath, out var typename);

        Assert.False(isPicture);
    }

    [Fact]
    public void IsPngFile()
    {
        var testFilePath = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "TestFiles", "logo.png");
        Assert.True(File.Exists(testFilePath));

        var isPng = ImagingHelper.IsPictureType(testFilePath, ImageType.Png);

        Assert.True(isPng);
    }

    [Fact]
    public void ShouldConvertPngToIcon()
    {
        var sourcePng = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "TestFiles", "logo.png");
        var destIco = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "TestFiles", "logo.ico");
        Assert.True(File.Exists(sourcePng));
        Assert.False(File.Exists(destIco));

        ImagingHelper.ConvertToIcon(sourcePng, destIco);

        Assert.True(File.Exists(destIco));
        File.Delete(destIco);
    }
}
```

用户确认选择好文件后，点击前面提过的按钮`立即转换并下载Icon`执行转换的方法`ConvertAndDownloadIcon()`，代码文件：[IcoTool.razor](https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools.Web/Pages/Public/ImageTools/IcoTool.razor)

```html
@code { private async Task ConvertAndDownloadIcon() { if (_sourceBrowserFile ==
null) return; _loading = true; var tempSourcePath = Path.GetTempFileName(); var
tempDestPath = Path.GetTempFileName(); try { var fileName =
$"{Path.GetFileNameWithoutExtension(_sourceBrowserFile.Name)}.ico"; await
UploadFile(tempSourcePath); ImagingHelper.ConvertToIcon(tempSourcePath,
tempDestPath); await DownloadFile(tempDestPath, fileName); } finally {
DeleteFile(tempSourcePath); DeleteFile(tempDestPath); _loading = false; } }
private async Task UploadFile(string saveFilePath) { await using var
sourceFileStream = new FileStream(saveFilePath, FileMode.Create); await
_sourceBrowserFile!.OpenReadStream().CopyToAsync(sourceFileStream); } private
async Task DownloadFile(string fromFilePath, string saveFileName) { await using
var destFileStream = new FileStream(fromFilePath, FileMode.Open); using var
streamRef = new DotNetStreamReference(destFileStream); await
Js.InvokeVoidAsync("downloadFileFromStream", saveFileName, streamRef); } private
void DeleteFile(string filePath) { try { if (File.Exists(filePath))
File.Delete(filePath); } catch { // ignored } } }
```

转换和下载过程中会临时保存上传的源文件和转换的 Icon 文件，下载完成后会立即删除这两个文件，请放心使用。

## 2.3 转换后的 Ico 文件下载

文件转换成功后，怎么提供下载呢？

起初想使用一个`<a href="/files/xxx.ico" target="_blank">xxx.ico</a>`标签提供浏览下载的，但动态生成的图片无法访问，不知道什么原因，只能暂时采用一个折衷的方式，有朋友有好的想法欢迎留言。

目前采用的是提供按钮下载，下面是封装的 js 下载方法，来自微软的文档：[ASP.NET Core Blazor file downloads](https://docs.microsoft.com/en-us/aspnet/core/blazor/file-downloads?view=aspnetcore-6.0)

我把`JS`代码放[\_Layout.cshtml](https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools.Web/Pages/_Layout.cshtml)：

```JavaScript
<script>
    // 省略部分代码

    async function downloadFileFromStream(fileName, contentStreamReference) {
        const arrayBuffer = await contentStreamReference.arrayBuffer();
        const blob = new Blob([arrayBuffer]);

        const url = URL.createObjectURL(blob);

        triggerFileDownload(fileName, url);

        URL.revokeObjectURL(url);
    }

    function triggerFileDownload(fileName, url) {
        const anchorElement = document.createElement('a');
        anchorElement.href = url;

        if (fileName) {
            anchorElement.download = fileName;
        }

        anchorElement.click();
        anchorElement.remove();
    }
</script>
```

页面下载时使用上面贴过的方法`DownloadFile(string fromFilePath, string saveFileName)`，这里不再贴代码。

下载文件使用到`JS互操作`(什么是`JS互操作`？可以参考我转载的这篇文章了解[(14/30)大家一起学 Blazor：JavaScript interop(互操作)](https://dotnet9.com/2021/12/14-of-30-let-us-learn-blazor-together-javascript-interop))

## 3. 总结

1. Blazor 组件库使用的[MASA Blazor](https://masa-blazor-docs-dev.lonsid.cn/)，很美观大方的`Material Design`设计风格。
2. Ico 转换，使用到了`System.Drawing.Common`包的`Bitmap`，.NET 6 开始不支持跨平台，提示只支持`Windows`平台,重点：**只是该包`System.Drawing.Common`不支持跨平台，不是.NET 6 不支持跨平台，.NET 6 支持跨平台**。
3. 本工具使用[7.0.100-preview.1](https://dotnet.microsoft.com/en-us/download/dotnet/7.0)开发、编译、上线，使用[.NET 6](https://dotnet.microsoft.com/en-us/download/dotnet/6.0)的同学，请放心使用，可以无缝升级。

`Dotnet9工具箱`会不断添加新的免费、开源、在线工具，欢迎 star 支持，有什么需求我会考虑加上，仓库地址：[Dotnet9.Tools](https://github.com/dotnet9/dotnet9.com)，可[提交 issue](https://github.com/dotnet9/dotnet9.com/issues/new)、[网站留言](https://dotnet9.com)、微信公众号(dotnet9)联系等等。

> 本工具源码：[IcoTool](https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools.Web/Pages/Public/ImageTools/IcoTool.razor)
>
> 介绍文章：[Blazor 在线 Ico 转换工具](https://dotnet9.com/?p=1715)
>
> 在线演示地址：[https://tool.dotnet9.com/ico](https://tool.dotnet9.com/ico)
