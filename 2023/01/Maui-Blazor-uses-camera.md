---
title: Maui Blazor 使用摄像头实现
slug: Maui-Blazor-uses-camera
description: 由于Maui Blazor中界面是由WebView渲染，所以再使用Android的摄像头时无法去获取，因为原生的摄像头需要绑定界面组件
date: 2023-01-12 20:51:15
copyright: Contributes
author: dotnet-simple
originalTitle: Maui Blazor 使用摄像头实现
originalLink: https://www.cnblogs.com/hejiale010426/p/17045707.html
draft: false
cover: https://img1.dotnet9.com/2023/01/cover_03.png
categories: 
    - MAUI
    - Blazor
tags: 
    - .NET
    - Blazor
    - MAUI
---

> 本文由网友投稿。
>
> 作者：dotnet-simple
>
> 原文标题：Maui Blazor 使用摄像头实现
>
> 原文链接：https://www.cnblogs.com/hejiale010426/p/17045707.html

由于`Maui Blazor`中界面是由`WebView`渲染，所以在使用 Android 的摄像头时无法去获取：因为原生的摄像头需要绑定界面组件。我找到了其他的实现方式，通过`WebView`使用`js`调用设备摄像头，支持多平台兼容，目前测试了`Android` 和`PC`， 由于没有`ios`和`macOS`无法测试，大概率是兼容的，可能需要动态申请权限。

1. 添加 js 方法

我们在`wwwroot`中创建一个`helper.js`的文件并且添加以下两个`js`函数。

在`index.html`中添加`<script src="helper.js"></script>`引入`js`。

```js
/**
 * 设置元素的src
 * @param {any} canvasId canvasId的dom id
 * @param {any} videoId video的dom id
 * @param {any} srcId img的dom id
 * @param {any} widht 设置截图宽度
 * @param {any} height 设置截图高度
 */
function setImgSrc(dest, videoId, srcId, widht, height) {
  let video = document.getElementById(videoId);
  let canvas = document.getElementById(canvasId);
  console.log(video.clientHeight, video.clientWidth);

  canvas.getContext("2d").drawImage(video, 0, 0, widht, height);

  canvas.toBlob(
    function (blob) {
      var src = document.getElementById(srcId);
      src.setAttribute("height", height);
      src.setAttribute("width", widht);
      src.setAttribute("src", URL.createObjectURL(blob));
    },
    "image/jpeg",
    0.95
  );
}

/**
 * 初始化摄像头
 * @param {any} src
 */
function startVideo(src) {
  if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
    navigator.mediaDevices
      .getUserMedia({ video: true })
      .then(function (stream) {
        let video = document.getElementById(src);
        if ("srcObject" in video) {
          video.srcObject = stream;
        } else {
          video.src = window.URL.createObjectURL(stream);
        }
        video.onloadedmetadata = function (e) {
          video.play();
        };
        //mirror image
        video.style.webkitTransform = "scaleX(-1)";
        video.style.transform = "scaleX(-1)";
      });
  }
}
```

然后各个平台的兼容。

**android：**

`Platforms/Android/AndroidManifest.xml`文件内容

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
	<application android:allowBackup="true" android:supportsRtl="true"></application>
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	<uses-permission android:name="android.permission.INTERNET" />
	<!--相机权限-->
	<uses-permission android:name="android.permission.CAMERA" android:required="false"/>
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
	<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
	<!--相机功能-->
	<uses-feature android:name="android.hardware.camera" />
	<!--音频录制权限-->
	<uses-permission android:name="android.permission.RECORD_AUDIO" />
	<!--位置权限-->
	<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

	<!-- Needed only if your app targets Android 5.0 (API level 21) or higher. -->
	<uses-feature android:name="android.hardware.location.gps" />

	<queries>
		<intent>
			<action android:name="android.intent.action.VIEW" />
			<data android:scheme="http"/>
		</intent>
		<intent>
			<action android:name="android.intent.action.VIEW" />
			<data android:scheme="https"/>
		</intent>
	</queries>
</manifest>
```

`Platforms/Android/MainActivity.cs`文件内容

```csharp
[Activity(Theme = "@style/Maui.SplashTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode | ConfigChanges.ScreenLayout | ConfigChanges.SmallestScreenSize | ConfigChanges.Density)]
public class MainActivity : MauiAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        Platform.Init(this, savedInstanceState);
        // 申请所需权限 也可以再使用的时候去申请
        ActivityCompat.RequestPermissions(this, new[] { Manifest.Permission.Camera, Manifest.Permission.RecordAudio, Manifest.Permission.ModifyAudioSettings }, 0);
    }
}
```

`MauiWebChromeClient.cs`文件内容

```csharp
#if ANDROID
using Android.Webkit;
using Microsoft.AspNetCore.Components.WebView.Maui;

namespace MainSample;

public class MauiWebChromeClient : WebChromeClient
{
    public override void OnPermissionRequest(PermissionRequest request)
    {
        request.Grant(request.GetResources());
    }
}

public class MauiBlazorWebViewHandler : BlazorWebViewHandler
{
    protected override void ConnectHandler(Android.Webkit.WebView platformView)
    {
        platformView.SetWebChromeClient(new MauiWebChromeClient());
        base.ConnectHandler(platformView);
    }
}

#endif
```

在`MauiProgram.cs`中添加以下代码；如果没有下面代码会出现没有摄像头权限，具体在这个 [issue](https://github.com/dotnet/maui/issues/3694) 体现

```csharp
#if ANDROID
builder.ConfigureMauiHandlers(handlers =>
{
    handlers.AddHandler<IBlazorWebView, MauiBlazorWebViewHandler>();
});
#endif
```

以上是`android`的适配代码， `pc`不需要设置额外代码，`ios`和`macOS`不清楚。

然后编写界面

```csharp
@page "/" @*界面路由*@

@inject IJSRuntime JSRuntime @*注入jsRuntime*@

@if(OpenCameraStatus) @*在摄像头没有被打开的情况不显示video*@
{
    <video id="@VideoId" width="@widht" height="@height" />
    <canvas class="d-none" id="@CanvasId" width="@widht" height="@height" />
}
<button @onclick="" style="margin:8px">打开摄像头</button>
@*因为摄像头必须是用户手动触发如果界面是滑动进入无法直接调用方法打开摄像头所以添加按钮触发*@
<button style="margin:8px">截图</button> @*对视频流截取一张图*@

<img id="@ImgId" />

@code{
    private string CanvasId;
    private string ImgId;
    private string VideoId;
    private bool OpenCameraStatus;
    private int widht = 320;
    private int height = 500;

    private async Task OpenCamera()
    {
        if (!OpenCameraStatus)
        {
            // 由于打开摄像头的条件必须是用户手动触发如果滑动切换到界面是无法触发的
            await JSRuntime.InvokeVoidAsync("startVideo", "videoFeed");
            OpenCameraStatus = true;
            StateHasChanged();
        }
    }

    protected override async Task OnInitializedAsync()
    {
        // 初始化id
        ImgId = Guid.NewGuid().ToString("N");
        CanvasId = Guid.NewGuid().ToString("N");
        VideoId = Guid.NewGuid().ToString("N");
        await base.OnInitializedAsync();
    }

    private async Task Screenshot()
    {
        await JSRuntime.InvokeAsync<String>("setImgSrc", CanvasId,VideoId, ImgId, widht, height);
    }
}
```

然后可以运行程序就可以看到我们的效果了。

![](https://img1.dotnet9.com/2023/01/0301.jpg)

示例代码：

- gitee：https://gitee.com/hejiale010426/main-sample
- github：https://github.com/239573049/main-sample

来着 token 的分享

技术交流群：737776595
