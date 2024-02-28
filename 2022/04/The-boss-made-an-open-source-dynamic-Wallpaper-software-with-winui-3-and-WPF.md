---
title: 大佬用 WinUI 3 和 WPF 做了一个开源的动态壁纸软件
slug: The-boss-made-an-open-source-dynamic-Wallpaper-software-with-winui-3-and-WPF
description: 说到动态壁纸软件，相信很多人都听说过或用过 `Wallpaper Engine`, 这是 Steam Store 一个好评如潮的壁纸软件，售价为 `18` 元。我之前也一直在使用，当然今天介绍的是另外一个壁纸软件 `Lively Wallpaper`。
date: 2022-04-04 11:21:39
copyright: Reprinted
author: SpringLeee 全球技术精选
originaltitle: 大佬用 WinUI 3 和 WPF 做了一个开源的动态壁纸软件
originallink: https://mp.weixin.qq.com/s/ACihOXY8v2l2qZmUxKw-ow
draft: False
cover: https://img1.dotnet9.com/2022/04/0301.gif
categories: .NET
tags: C#,Winform,WPF,UWP,WinUI,动态壁纸,开源WPF
---

说到动态壁纸软件，相信很多人都听说过或用过 `Wallpaper Engine`, 这是 Steam Store 一个好评如潮的壁纸软件，售价为 `18` 元。我之前也一直在使用，当然今天介绍的是另外一个壁纸软件 `Lively Wallpaper`。

## 1. Lively Wallpaper   

Lively 是一个动态壁纸软件，完全开源且免费，主要由 Dani John 开发，支持让用户设置动画桌面壁纸和屏幕保护程序，支持多显示器。使用 .net core 开发，目前 Lively 的核心是用 WPF 编写的，而 UI 则使用的是 WinUI3。

![](https://img1.dotnet9.com/2022/04/0301.gif)

使用 Lively Wallpaper 非常简单和方便，它还支持设置图片，视频，网页，Unity、Godot 游戏作为壁纸。

![](https://img1.dotnet9.com/2022/04/0302.gif)


## 2. 屏幕保护程序

![](https://img1.dotnet9.com/2022/04/0303.gif)


Lively 还支持设置 Windows 屏幕保护程序。

## 3. 自动化      

![](https://img1.dotnet9.com/2022/04/0304.gif)


Lively 还可以通过传入命令行参数的方式控制壁纸和自定义。

## 4. Lively API     

![](https://img1.dotnet9.com/2022/04/0305.gif)


Lively 提供了强大的 API，很方便的获取鼠标、键盘等事件，以及系统信息，可以让开发人员创造交互式的壁纸。

## 5. 自定义任务栏    

![](https://img1.dotnet9.com/2022/04/0306.gif)


Lively 还支持自定义任务栏，支持 Win 11，,它有一个可选择的主题，包括透明、模糊、高斯模糊，半透明等，让您的任务栏和桌面壁纸融为一体！

## 6. 开发历程      

实际上 Lively 已经开发几年了，不过这个开发过程相当的魔幻，作者为了追求高性能和流畅度， 这几年按照 Winform -> WPF -> UWP -> WinUI3 的顺序重写了 UI 。

不知道微软明年会不会推出新的 UI 框架, 哈哈.....

<video id="video" controls="" preload="none" poster="https://img1.dotnet9.com/2022/04/0309.png">
  <source id="mp4" src="https://img1.dotnet9.com/2022/04/0308.mp4" type="video/mp4">
</video>

## 7. 下载安装      

- Github 下载 [https://github.com/rocksdanister/lively/releases](https://github.com/rocksdanister/lively/releases)

![](https://img1.dotnet9.com/2022/04/0310.png)

- Windows 应用商店

![](https://img1.dotnet9.com/2022/04/0307.png)