---
title: WinForm和WPF有什么区别?
slug: What-is-the-difference-between-WinForm-and-WPF
description: 总有小伙伴问: WinForm和WPF有什么区别? 细想这个问题好像很简单回答,但是总是没有系统的分析过,今天抽空特地写一篇仅代表个人观点的文章记录总结。
date: 2022-04-07 19:56:46
copyright: Reprinted
author: DotNetOneByOne
originaltitle: WinForm和WPF有什么区别?
originallink: https://mp.weixin.qq.com/s/IXpv_mNtR5gjO1IlUXAtpg
draft: False
cover: https://img1.dotnet9.com/2022/04/0501.jpg
categories: .NET
tags: Winform, WPF
---

总有小伙伴问: “WinForm和WPF有什么区别?”

细想这个问题好像很简单回答,但是总是没有系统的分析过,今天抽空特地写一篇仅代表个人观点的文章记录总结。

![](https://img1.dotnet9.com/2022/04/0501.jpg)

## WinForms

顾名思义，基本上是一种引入 .NET 框架的基于 GUI 的方法。在 WPF 和 Silverlight 之前，它是用于构建 GUI 的 .NET 的主要 API。除了运行时和操作系统之外，它不需要任何类型的支持来开发独立的应用程序。可以开发易于部署、更新、管理和离线工作的应用程序，同时连接到 Internet。WinForms 的开发非常简单，因为它只是基于 UI 控件在画布上的拖放放置。它是开发桌面应用程序的旧平台。

## WPF（Windows Presentation Foundation）

WPF，顾名思义，是用于开发 Windows 或桌面客户端应用程序的 UI 框架。它是与 .NET 框架一起使用的 GUI 框架的最新方法。引入它是为了开发在 Windows 操作系统上运行的 Windows 客户端应用程序，以及下一代 Windows 窗体。它具有开发、运行、执行、管理和处理 Windows 客户端应用程序所需的所有功能。

## 上手难度不同

windowform的难度比wpf相对低，因为wpf你要学习xaml的语法,还要学习mvvm,而winform 大多数只需要拖拉拽控件即可快速上手一个项目.

## 渲染机制不同

WinForm GDI+绘制,WPF DirectX 渲染绘制

>GDI + :编写图形程序时需要使用GDI（GraphicsDeviceInterface，图形设备接口），从程序设计的角度看，GDI包括两部分：一部分是GDI对象，另一部分是GDI函数。GDI对象定义了GDI函数使用的工具和环境变量，而GDI函数使用GDI对象绘制各种图形，在C#中，进行图形程序编写时用到的是GDI+（GraphiceDeviceInterfacePlus图形设备接口）版本，GDI+是GDI的进一步扩展，它使我们编程更加方便。`简单理解就是2D绘图`

>DirectX（Direct eXtension，缩写：DX）是由微软公司创建的一系列专为多媒体以及游戏开发的应用程序接口。旗下包含Direct3D、Direct2D、DirectCompute等等多个不同用途的子部分，因为这一系列API皆以Direct字样开头，所以DirectX（只要把X字母替换为任何一个特定API的名字）就成为这一巨大的API系列的统称。`简单理解就是既能2D,也能3D绘图`

`简单理解,WPF理论上可以写更牛X的界面。渲染速度更快,复杂度更高`

## 核心机制不同

Winform 事件驱动,WPF 数据驱动

>数据驱动：数据第一，控件第二。数据的变化带动UI，`便于前后端解耦`。

>事件驱动：通过事件绑定方式,实现控件各项事件的触发来调用业务层逻辑使程序有序运行，`极容易造成前后端高耦合`。

## 控件存在形式

在Windows GDI或WinForm开发中复杂的GUI应用程序，会使用的大量的控件，如Grid等。而每个控件或Grid cell都是一个小窗口，会使用一个Window handle，尽管控件厂商提供了很多优化办法，但还是会碰到Out of Memory或"Error Create Window handle"，而导致程序退出。

WPF彻底改变了控件显示的模式，控件不再使用窗口，也就不会占用Window handle。理论上，如果一个WPF只有一个主窗口的话，WPF只会使用一个Window handle（如果忽略用于Dispatcher的隐藏窗口的话）。所以WPF GUI程序不会出现Window handle不够用的情况。

## WinForm VS WPF

| 区别点 | WinForms | WPF |
|--------|--------|--------|
| 渲染方式 | GDI+ | DirectX |
| 渲染速度 | 慢 | 快 |
| 上手难度 | 普通 | 较为困难 |
| 驱动机制 | 事件驱动 | 数据驱动 |
| 前后端是否分离 | 不易分离 | 较易分离 |
| 自适应 | 较为困难 | 容易 |
| 提供矢量 2D 和 3D 功能 | 否 | 是 |
| 需要内存 | 少 | 多 |
| 支持界面虚拟化，方便处理大型数据集。 | 不支持 | 支持 |
| 控件以窗口形式存在 | 是 | 否 |