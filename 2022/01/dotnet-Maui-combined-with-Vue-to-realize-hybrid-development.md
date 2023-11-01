---
title: .NET MAUI 中结合 Vue 实现混合开发
slug: dotnet-Maui-combined-with-Vue-to-realize-hybrid-development
description: 在MAUI微软的官方方案是使用Blazor开发，但是当前市场大多数的Web项目使用Vue，React等技术构建，如果我们没法绕过已经积累的技术，用Blazor重写整个项目并不现实。
date: 2022-01-18 22:11:31
copyright: Reprinted
author: 林 小
originaltitle: .NET MAUI 中结合 Vue 实现混合开发
originallink: https://www.cnblogs.com/jevonsflash/p/15806237.html
draft: False
cover: https://img1.dotnet9.com/2022/01/cover_05.png
categories: MAUI
tags: .NET,MAUI,Vue,混合开发
---

在MAUI微软的官方方案是使用Blazor开发，但是当前市场大多数的Web项目使用Vue，React等技术构建，如果我们没法绕过已经积累的技术，用Blazor重写整个项目并不现实。

Vue是当前流行的web框架， 简单来说是一套模板引擎，利用“模板”和“绑定”两大特性实现web页面mvvm模式开发。利用.NET MAUI框架可以将Vue应用嵌入到Web容器中。可以实现跨平台的混合开发。

例如我在某医疗行业项目中，已经用这个混合开发的方式生成应用，Vue代码不需要做什么改动，就能跨平台运行：

![](https://img1.dotnet9.com/2022/01/cover_05.png)

如果你有一套Vue开发的网站，可以根据这篇文章，尝试移值进你的iPhone，Android以及平板电脑等移动设备。

混合开发的核心工作是构建Web与.net 的互操作，我们将利用Blazor引擎的如下功能：

- 资源的统一管理
- js代码的注入
- js调用C#代码
- C#调用js代码

如果你还不了解混合开发的概念，请回看上一章节[[MAUI] 混合开发概念_jevonsflash的专栏-CSDN博客](https://blog.csdn.net/jevonsflash/article/details/121835547)

整个工作分为MAUI部分，Vue部分和混合改造。

## MAUI部分

创建Maui App项目：

![](https://img1.dotnet9.com/2022/01/0501.png)

你也可以创建 Maui Blazor App 项目，命名为MatoProject，但是这个模板主要围绕Blazor开发，有的功能我们并不需要，得删很多文件。

创建完成后编辑MatoProject.csproj，在Sdk最末尾加上.Razor，VS会自动安装Microsoft.AspNetCore.Components.WebView.Maui依赖包（注意不要手动Nuget添加这个包，否则程序无法运行）

![](https://img1.dotnet9.com/2022/01/0502.png)

![](https://img1.dotnet9.com/2022/01/0503.png)

安装完成后在项目目录中创建一个wwwroot文件夹

![](https://img1.dotnet9.com/2022/01/0504.png)

这个文件夹将是混合开发Web部分的根目录，这个名称不能随便定义，我们看看为什么：

打开Microsoft.AspNetCore.Components.WebView.Maui.targets这个文件：

![](https://img1.dotnet9.com/2022/01/0505.png)

我们可以看到构建项目时，这个库会将wwwroot文件夹里的内容作为Maui资源（MauiAsset）类型设置标签，编译器则会根据MauiAsset标签将这些内容打包进各个平台的资源文件夹，具体的Maui资源类型可以参考这个文章[.NET MAUI – Manage App Resources – Developer Thoughts (egvijayanand.in)](https://egvijayanand.in/2021/08/20/net-maui-manage-app-resources/) ，

打开MauiProgram.cs 在builder 中注册BlazorMauiWebView组件，在服务中使用扩展方法AddBlazorWebView()来添加相关Blazor的服务

```C#
using Microsoft.Maui;
using Microsoft.Maui.Hosting;
using Microsoft.Maui.Controls.Compatibility;
using Microsoft.Maui.Controls.Hosting;
using Microsoft.AspNetCore.Components.WebView.Maui;
using Microsoft.Extensions.DependencyInjection;

namespace MatoProject
{
	public static class MauiProgram
	{
		public static MauiApp CreateMauiApp()
		{
			var builder = MauiApp.CreateBuilder();
			builder
				.RegisterBlazorMauiWebView()
				.UseMauiApp<App>()
				.ConfigureFonts(fonts =>
				{
					fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular");
				});
			builder.Services.AddBlazorWebView();
			return builder.Build();
		}
	}
}
```

打开MainPage.xaml，编辑原生应用的主页面：

建立BlazorWebView控件铺满屏幕，并设置HostPage为Web部分的主页index.html

```html
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MatoProject.MainPage"
             xmlns:b="clr-namespace:Microsoft.AspNetCore.Components.WebView.Maui;assembly=Microsoft.AspNetCore.Components.WebView.Maui"
             BackgroundColor="{DynamicResource SecondaryColor}">

    <Grid>
        <b:BlazorWebView HostPage="wwwroot/index.html">
            <b:BlazorWebView.RootComponents>
                <b:RootComponent Selector="#blazorapp" x:Name="MainWebView" ComponentType="{x:Type local:Index}/>
            </b:BlazorWebView.RootComponents>
        </b:BlazorWebView>
    </Grid>
</ContentPage>
```

建立_import.razor

```html
@using System.Net.Http
@using Microsoft.AspNetCore.Components.Forms
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.Web
@using Microsoft.AspNetCore.Components.Web.Virtualization
@using Microsoft.JSInterop
@using MatoProject
```

## Vue部分

至此我们建立好了原生开发的Web容器，接下来需要处理Vue项目了：

cd到项目目录，使用vue-cli创建一个空白Vue项目：

![](https://img1.dotnet9.com/2022/01/0506.png)

 这里可以按照Vue的编程喜好建立，比如我选择了2.0项目，支持Typescript，es6的class命名方式等，最终都要通过webpack打包成静态资源，所以无所谓。

建立src/api/fooService.ts，创建如下的函数：

window['DotNet']对象将是MAUI Blazor中注入的交互操作对象

```js
export async function GetAll(data) {
    var result = null
        await window['DotNet'].invokeMethodAsync('MatoProject', 'GetFoo')
            .then(data => {
                console.log("DotNet method return the value:" + data);
                result = data
            });
    return result
}


export async function Add(data) {
    var result = null
        await window['DotNet'].invokeMethodAsync('MatoProject', 'Add', data)
            .then(data => {
                console.log("DotNet method return the value:" + data);
                result = data
            });
    return result
}
```

打开Home.vue 编辑：

这是Web的主页面，我们需要三个按钮以及相关函数，测试js与C#的交互操作。

```vue
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <div>
      <h3>foo:</h3>
      <button @click="getFoo">click to get foo</button>
      <br />
      <span>{{ foo }}</span>
    </div>
    <div>
      <h3>bar:</h3>
      <span>{{ bar }}</span>
    </div>
    <div>
      <button @click="add">click here to add</button>
      <span>click count:{{ cnt }}</span>
    </div>
  </div>
</template>
```

```typescript

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import HelloWorld from "@/components/HelloWorld.vue"; // @ is an alias to /src
import { GetAll, Add } from "@/api/fooService";

@Component({
  components: {
    HelloWorld,
  },
})
export default class Home extends Vue {
  foo: string = "";
  bar: string = "";
  cnt: number = 0;

  async created() {
    window["postBar"] = this.postBar;
  }
  async add() {
    this.cnt = await Add({ a: this.cnt, b: 1 });
  }

  async getFoo() {
    var foo = await GetAll(null);
    this.foo = foo;
  }

  async postBar(data) {
    this.bar = data;
    console.log("DotNet invocked the function with param:" + data);
    return this.bar;
  }
}
</script>
```

到此已经完成了一个简单的Vue项目

运行打包命令：

```shell
PS D:\Project\maui-vue-hybirddev\hybird-host> yarn build
```

将dist目录中的所有内容复制到wwwroot文件夹下。

## 混合改造

这是混合开发的重点，改造MAUI项目，以适配Vue

打开wwwroot/index.js重写为：

```html
<!DOCTYPE html>
<html lang="">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link rel="icon" href="favicon.ico">
    <title>hybird-host</title>
    <link href="js/about.dc8b0f2b.js" rel="prefetch">
    <link href="css/app.03043124.css" rel="preload" as="style">
    <link href="js/app.b6b5425b.js" rel="preload" as="script" crossorigin="anonymous">
    <link href="js/chunk-vendors.cf6d8f84.js" rel="preload" as="script" crossorigin="anonymous">
    <link href="css/app.03043124.css" rel="stylesheet">
</head>
<body>
    <div id="blazorapp">Loading...</div>
    <script src="_framework/blazor.webview.js" autostart="false"></script>
</body>
</html>
```

**注意，仅全部重写body部分，不要更改head的link标签内容，仅在js后面加上crossorigin="anonymous" 以解决跨域问题。**

建立Index.razor文件：

```html
@using Microsoft.Maui.Controls
@inject IJSRuntime JSRuntime
@implements IDisposable
<noscript><strong>We're sorry but CareAtHome doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript><div id="app"></div>
@code {

    [JSInvokable]
    public static Task<string> GetFoo()
    {
        return Task.FromResult("this is foo call C# method from js");
    }

    [JSInvokable]
    public static Task<int> Add(AddInput addInput)
    {
        return Task.FromResult(addInput.a + addInput.b);
    }

    public async void Post(object o, EventArgs a)
    {
        await JSRuntime.InvokeAsync<string>("postBar", "this is bar call js method from C#");
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        ((App.Current as App).MainPage as MainPage).OnPostBar += this.Post;
        try
        {
            if (firstRender)
            {
                await JSRuntime.InvokeAsync<IJSObjectReference>("import", "./js/chunk-vendors.cf6d8f84.js", new { crossorigin = "anonymous" });
                await JSRuntime.InvokeAsync<IJSObjectReference>("import", "./js/app.b6b5425b.js", new { crossorigin = "anonymous" });
            }


        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }

    }

    public void Dispose()
    {
        (Application.Current.MainPage as MainPage).OnPostBar -= this.Post;
    }


}
```

注意以下这两个语句需要对应打包生成的实际文件名，并且加上跨域标签

```C#
await JSRuntime.InvokeAsync<IJSObjectReference>("import", "./js/chunk-vendors.cf6d8f84.js", new { crossorigin = "anonymous" });
await JSRuntime.InvokeAsync<IJSObjectReference>("import", "./js/app.b6b5425b.js", new { crossorigin = "anonymous" });
```

MainPage.xaml建立一个按钮并且设置触发事件方法：

```html
<Button Text="Post Bar To WebView" HorizontalOptions="Center" VerticalOptions="End" HeightRequest="40" Clicked="PostBar_Clicked"></Button>
```

CodeBehind:

```C#
using System;
using Microsoft.Maui.Controls;
using Microsoft.Maui.Essentials;

namespace MatoProject
{
	public partial class MainPage : ContentPage
	{
        public event EventHandler<EventArgs> OnPostBar;

		int count = 0;

		public MainPage()
		{
			InitializeComponent();
		}

		private async void PostBar_Clicked(object sender, EventArgs args)
		{
			OnPostBar?.Invoke(this, args);
		}
	}
}
```

至此，所有的代码工作已经完成，在PC上可以选择Windows或者Android模拟器来运行程序

![](https://img1.dotnet9.com/2022/01/0507.png)

运行效果：

![](https://img1.dotnet9.com/2022/01/0508.png)

若在windows平台上运行，原生控件使用 Edge WebView2 呈现器加载页面， 按f12会调用原生的调试工具，在这里看到打印

![](https://img1.dotnet9.com/2022/01/0509.png)

现在，可能有人会问为什么要使用这样的技术架构？明明可能有更好用的混合开发技术Ionic，React Native，Uni-app。首先不可否认这些技术都有他们的特点与优势，但当你拥有一个成熟的Xamarin框架，你可以轻松迁移到MAUI，利用EFCore实现数据持久化或者集成Abp框架来配置依赖注入，全局事件，本地化等移动开发常用的功能（另一篇文章将会教大家如何将Abp移值进MAUI）。Xamarin是一个设备抽象层，提供的WebView也有较好的H5兼容性。

当然主要原因还是在快速开发上，你的代码积累才是宝贵的，更少的修改代码量才是王道，如果你在用React技术栈编写Web代码，也许React Native才是你最佳选择 。**没有最优的技术，只有最适合你的技术。**

**代码仓库：**

- [jevonsflash/maui-vue-hybirddev: maui-vue-hybirddev (github.com)](https://github.com/jevonsflash/maui-vue-hybirddev)
- [jevonsflash/maui-vue-hybirddev (gitee.com)](https://gitee.com/jevonsflash/maui-vue-hybirddev)