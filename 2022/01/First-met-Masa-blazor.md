---
title: 初识MASA Blazor
slug: First-met-Masa-blazor
description: 一个Mateial风格的开源Blazor组件库
date: 2022-01-12 22:12:04
copyright: Reprinted
author: MASA技术团队
originaltitle: 初识MASA Blazor
originallink: https://www.cnblogs.com/MASA/p/15766886.html
draft: False
cover: https://img1.dotnet9.com/2022/01/0711.png
albums: 开源Blazor
categories: Blazor
tags: C#,Blazor
---

MASA Blazor是一个Blazor的UI组件库。就像大家写前端熟知的Bootstrap, Ant Design一样。

>MASA Blazor官网地址：[https://blazor.masastack.com](https://blazor.masastack.com)
>
>MASA Blazor Github地址：[https://github.com/BlazorComponent/MASA.Blazor](https://github.com/BlazorComponent/MASA.Blazor)
>
>MASA Blazor Pro演示地址：[https://blazor-pro.masastack.com/dashboards/ecommerce](https://blazor-pro.masastack.com/dashboards/ecommerce)
>
>MASA Blazor Pro Github地址：[https://github.com/BlazorComponent/MASA.Blazor.Pro](https://github.com/BlazorComponent/MASA.Blazor.Pro)

## 什么是Blazor

在介绍MASA Blazor之前，有必要知道一下什么是Blazor，如果已经了解Blazor是什么的小伙伴可以直接跳过，往下面继续看。

Blazor 是一个使用 .NET生成交互式客户端 Web UI 的框架，使用Blazor，开发人员可以使用C#语言在服务器和客户端进行编码，构建丰富Web应用程序。听起来是不是很不错，不熟悉前端开发语言的后端小伙伴，也可以使用Blazor进行Web应用开发了。简单看一下官方的描述。

![](https://img1.dotnet9.com/2022/01/0701.png)

如果想深入了解Blazor的小伙伴可以翻一下[这篇文章](https://blogs.masastack.com/2021/10/25/masa/framework/masa-blazor/0/)，里面非常详细的介绍了什么是Blazor和Blazor与其他前端技术的对比。或者可以直接去翻[官方文档](https://docs.microsoft.com/zh-cn/aspnet/core/blazor/?view=aspnetcore-6.0)，这里我就不过多的阐述了。

## Material Design

了解完Blazor再来简单了解一下Material Design，为什么是Material Design，因为MASA Blazor就是基于Material Design设计的。

**简介：**Material Design，中文名：材料设计语言，有人也叫它质感设计，是由Google推出的跨平台设计语言，其旨在为手机、平板电脑、台式机和“其他平台”提供高度一致使用体验和视觉效果。

**优点：**Material Design包含了在设计时如何使用阴影、如何使用动画、甚至使用多少像素的边框都给出了建议，它可以帮助我们搭建更加合理的页面结构，如何排版、字体大小和间距，它定义了优雅流畅的交互效果引导用户在页面中的视觉焦点和交互操作。

Material Design的核心就是简洁。大而简、简而精。它把物理世界的体验带进屏幕。去掉现实中的杂质和随机性，保留其最原始纯净的形态、空间关系、变化与过渡，配合虚拟世界的灵活特性，还原最贴近真实的体验，达到简洁与直观的效果。它的优点当然不止这些。海外的很多应用包括大量web网站都是基于MD的设计。在国外还是非常火的。想了解更多关于MD的小伙伴可以去翻一下它的[官方网站](https://www.material.io/)，这里就先介绍到这。

## Vuetify

Vuetify 是一个在世界范围内广受欢迎的VueUI框架，它是建立在Vue.js之上的完备的界面框架。与其他框架不同。 Vuetify从一开始就设计为易于学习的并且拥有来自 [Material设计规范](https://material.io/) 的数百个精心设计组件。Vuetify采用移动优先的设计，这意味着无论是在手机、平板电脑还是台式电脑上，你的应用程序都可以开箱即用。

所以使用vue的小伙伴可以使用Vuetify很轻松的就构建出一套基于Material Design设计风格的应用。

是不是很爽呢？说了这么多都是人家前端的东西，那我们Blazor有这样的UI组件库吗？答案是当然有，那就是MASA Blazor。终于轮到了我们的主角。

## MASA Blazor

MASA Blazor开篇已经说过了，他是Blazor的一个UI组件库，Blazor使你脱离了JavaScript进行web应用开发，而MASA Blazor可以让你不必写，或者写很少的Css就能构建出一个非常漂亮的web应用。MASA Blazor就是严格按照Material Design的设计规范精心设计的UI组件库。接下来说一下他的优点。

1. 基于Material Design的设计风格，拥有Material Design的所有设计优点，多端体验、交互、操作优秀
2. 与Blazor深度集成，让后端开发人员能非常轻松的上手。
3. 天生具备响应式，除Vuetify有的组件外，还有许多预置组件如Url、面包屑、导航三联动，高级搜索，i18n等
4. 社区活跃、简易上手，全职团队维护

MASA Blazor深度还原了Vuetify的所有组件，做到了1:1还原之外还增加了非常实用的预置组件，对常用的一些组件或一组组件进行封装，更贴合开发者的日常使用，大大缩减了开发时间，增加了开发的效率。他同时也解决了大部分后端人调样式的烦恼。几句代码就能快速构建出来一个漂亮的页面。那么接下来我们就看一看如何在Blazor项目中引入它。

## 使用MASA Blazor

- 环境：.NET 6.0.0 + Visual Studio 2022
- 创建Blazor Server应用

```shell
dotnet new blazorserver -o BlazorServerApp
```

- 添加MASA.Blazor包

可以使用Nuget包管理工具搜索MASA.Blazor添加，也可以直接

```shell
dotnet add package MASA.Blazor
```

![](https://img1.dotnet9.com/2022/01/0702.png)

- 在Program.cs里添加Masa Blazor相关服务。

```C#
builder.Services.AddMasaBlazor();
```

>这里使用的是.net 6.0 Minimal Api没有了Startup类。代码看起来也比较简洁。如果是之前的.net版本就在Startup.ConfigureServices里添加services.AddMasaBlazor();就好了。

- Pages/_Layout.cshtml 中引入样式、字体、脚本

在head里添加样式和字体

```html
<link href="_content/MASA.Blazor/css/masa-blazor.css" rel="stylesheet">
<link href="_content/MASA.Blazor/css/masa-extend-blazor.css" rel="stylesheet">
<link href="https://cdn.jsdelivr.net/npm/@("@mdi")/font@5.x/css/materialdesignicons.min.css" rel="stylesheet">
<link href="https://fonts.googleapis.com/css?family=Material+Icons" rel="stylesheet">
<link href="https://use.fontawesome.com/releases/v5.0.13/css/all.css" rel="stylesheet">	
```

在body的底部添加js脚本

```html
<script src="_content/BlazorComponent/js/blazor-component.js"></script>
```

![](https://img1.dotnet9.com/2022/01/0703.png)

>如果是.net6.0之前的版本，这些代码是在Pages/_Host.cshtml(Server) 中

在_Imports.razor文件中引用MASA.Blazor，BlazorComponent明明空间，这样我们就不用每个文件都去引用一边了

```html
@using MASA.Blazor
@using BlazorComponent
```

![](https://img1.dotnet9.com/2022/01/0704.png)

替换Shared/MainLayout.razor里面的内容

```html
<MApp>
	@Body
</MApp>
```

![](https://img1.dotnet9.com/2022/01/0705.png)

### 使用模板快速创建Blazor项目

经过上述的步骤你就可以基于MASA Blazor进行开发了。但是步骤是不是有一点点多，些许繁杂？当然这些MASA Blazor也替我们考虑到了，所以它提供了更简单的安装方式，通过模板进行安装。

1. 通过dotnet命令安装模板

```shell
dotnet new --install MASA.Template
```

2. 创建基于MASA Blazor组件库的Blazor Server项目

```shell
dotnet new masab -o BlazorServerApp
```

简单两步就搞定了。这样创建完的项目就帮你完成了上述的几个步骤。

### 效果展示

接下来我们就用MASA Blazor的侧边栏和顶部工具栏替换掉原来demo的组件。部分代码截图：

>使用到了MNavigationDrawer和MAppBar两个组件。

![](https://img1.dotnet9.com/2022/01/0706.png)

效果图如下：

![](https://img1.dotnet9.com/2022/01/0707.png)

还有很多漂亮有趣的组件官网上都有示例和代码，比如这个AppBar：

![](https://img1.dotnet9.com/2022/01/0708.png)

点击查看源代码，然后把代码复制过来，一个组件就完成了。

![](https://img1.dotnet9.com/2022/01/0709.png)

看下效果：

![](https://img1.dotnet9.com/2022/01/0710.png)

就是这么简单。

我们再来看一下官方给的Admin模板（MASA Blazor Pro）效果如何：

![](https://img1.dotnet9.com/2022/01/0711.png)

![](https://img1.dotnet9.com/2022/01/0712.png)

MASA Blazor Pro 演示了大多数MASA Blazor组件的使用。而且是一个开箱即用的后台前端管理模板。都是开源免费的。 感兴趣的小伙伴可以去解一下。

>MASA Blazor官网地址：[https://blazor.masastack.com](https://blazor.masastack.com)
>
>MASA Blazor Github地址：[https://github.com/BlazorComponent/MASA.Blazor](https://github.com/BlazorComponent/MASA.Blazor)
>
>MASA Blazor Pro演示地址：[https://blazor-pro.masastack.com/dashboards/ecommerce](https://blazor-pro.masastack.com/dashboards/ecommerce)
>
>MASA Blazor Pro Github地址：[https://github.com/BlazorComponent/MASA.Blazor.Pro](https://github.com/BlazorComponent/MASA.Blazor.Pro)

## 我们正在行动，新的框架、新的生态

我们的目标是`自由的`、`易用的`、`可塑性强的`、`功能丰富的`、`健壮的`。

所以我们借鉴Building blocks的设计理念，正在做一个新的框架`MASA Framework`，它有哪些特点呢？

- 原生支持Dapr，且允许将Dapr替换成传统通信方式
- 架构不限，单体应用、SOA、微服务都支持
- 支持.Net原生框架，降低学习负担，除特定领域必须引入的概念，坚持不造新轮子
- 丰富的生态支持，除了框架以外还有组件库、权限中心、配置中心、故障排查中心、报警中心等一系列产品
- 核心代码库的单元测试覆盖率90%+
- 开源、免费、社区驱动
- 还有什么？我们在等你，一起来讨论

经过几个月的生产项目实践，已完成POC，目前正在把之前的积累重构到新的开源项目中

目前源码已开始同步到Github（文档站点在规划中，会慢慢完善起来）：

- [MASA.BuildingBlocks](https://github.com/masastack/MASA.BuildingBlocks)
- [MASA.Contrib](https://github.com/masastack/MASA.Contrib)
- [MASA.Utils](https://github.com/masastack/MASA.Utils)
- [MASA.EShop](https://github.com/masalabs/MASA.EShop)
- [BlazorComponent](https://github.com/blazorcomponent/BlazorComponent)
- [MASA.Blazor](https://github.com/blazorcomponent/MASA.Blazor)

**技术交流：**

- QQ群：[7424099](http://wpa.qq.com/msgrd?v=3&uin=7424099&site=qq&menu=yes)
- 微信群：加技术运营微信（MasaStackTechOps），备注来意，邀请进群

![](https://img1.dotnet9.com/2022/01/0713.png)

​ ------ END ------

**作者简介**

- 阎鹏举：MASA技术团队成员。