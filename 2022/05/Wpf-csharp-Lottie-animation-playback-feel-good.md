---
title: WPF|C# Lottie 动画播放【感觉好牛】
slug: Wpf-csharp-Lottie-animation-playback-feel-good
description: Lottie 动画是一种以 json 文件保存的动画文件。相比 gif 在文件体积和动画流畅度上有很大的优势，而且是图形是矢量的，放大不会糊。
date: 2022-05-19 06:58:14
copyright: Reprinted
author: xiejiang
originalTitle: WPF|C# Lottie 动画播放【感觉好牛】
originalLink: https://www.cnblogs.com/8u7tgyjire7890/p/15881159.html
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_49.jpg
categories: 
    - WPF
tags: 
    - WPF
    - 动画
---

[Lottie](https://lottiefiles.com/) 动画是一种以 json 文件保存的动画文件。 相比 gif 在文件体积和动画流畅度上有很大的优势，而且是图形是矢量的，放大不会糊。

Lottie 的官方提供了 web,android,iOS 的实现, 微软提供了 UWP 的实现 [Lottie-Windows](https://github.com/CommunityToolkit/Lottie-Windows)。 WPF 则~~没有找到可用的实现~~。下有 [https://github.com/ascora/LottieSharp](https://github.com/ascora/LottieSharp) 不过我测试了几轮，发现其依赖的 sharpDx 经常报错，而且我完全不熟悉 sharpDx，不知道如何解决。

那么作为 WPF 开发者，想要使用 Lottie 动画看来还是要自己动手。

想要在 WPF 上使用 Lottie 动画,主要需要攻克以下问题:

1. 解析 Lottie 的 json 文件, 了解其中所有数据的意义, 也就是得到需要绘制的图形对象, 包括各种圆形, 矩形, 颜色, 模糊效果, 遮罩, 缓动, 轨迹, 关键帧等。
2. 将上面得到的数据转换为具体的绘图参数。
3. 在 WPF 中进行高帧率的绘制。

截至 2022-02-10, 觉得可行的方案为:

1. 微软提供的 UWP 的实现中, 有现成的解析模块, 可直接将 Json 文件读取为一个数据类, 且该模块并没有依赖 UWP, 所以可以直接拿来用。虽然还有一些 Bug(测试了几十个动画文件, 有 3 个无法正常播放), 但当前该库还比较活跃, 可以期待其改进。
2. SkiaSharp 的功能非常全面, 覆盖 Lottie 动画所需的功能应该不成问题, 而且其跨平台, 所以按照数据要求使用 SkiaSharp 进行绘制即可。
3. SkiaSharp 官方提供的 WPF 画布即使开启 GPU 绘图模式, 其速度依然无法达到 1080p 60 帧, 不过之前进行其它项目时对 [GLWpfControl](https://github.com/opentk/GLWpfControl) 进行了改进, 实测 2k 144 帧占会用 RTX 2070 23%, 达到可用程度, 虽然有少量内存泄漏, 但问题不大。

如果成功实现的话, 应该可以同时支持 WinForm、WPF、UnoPlatform(GTK)、Avalonia、Blazor(Wasm) 以及所有 SkiaSharp 和 .Net Core 3.0+ 可以运行的平台上.

~~当前仍然处于探索验证阶段,无实用价值。~~

当前处于预览状态, 示例: [https://github.com/xiejiang2014/Xiejiang.SKLottie.Samples](https://github.com/xiejiang2014/Xiejiang.SKLottie.Samples)

本文发布于[https://www.cnblogs.com/8u7tgyjire7890/p/15881159.html](https://www.cnblogs.com/8u7tgyjire7890/p/15881159.html)

以下会持续更新成功实现的动画,每次更新都说明支持了更多的 lottie 特性

2022-02-10 选了几个简单的动画进行测试.

[https://lottiefiles.com/93494-step-loader](https://lottiefiles.com/93494-step-loader)

![](https://img1.dotnet9.com/2022/05/4901.gif)

[https://lottiefiles.com/87474-duo-cubes-loader-2](https://lottiefiles.com/87474-duo-cubes-loader-2)

![](https://img1.dotnet9.com/2022/05/4902.gif)

混合模式

[https://lottiefiles.com/89023-loading-circles](https://lottiefiles.com/89023-loading-circles)

![](https://img1.dotnet9.com/2022/05/4903.gif)

2022-02-12 稍微复杂一点的例子:

[https://lottiefiles.com/84848-polar-bear](https://lottiefiles.com/84848-polar-bear)

Lottie-Windows 没有实现 PolyStar 的数据解析,所以缺少黄色的星星,只能等待 Lottie-Windows 继续实现

![](https://img1.dotnet9.com/2022/05/4904.gif)

路径动画.

[https://lottiefiles.com/88348-blob](https://lottiefiles.com/88348-blob)

![](https://img1.dotnet9.com/2022/05/4905.gif)

路径偏移

[https://lottiefiles.com/86095-frequencies-fork](https://lottiefiles.com/86095-frequencies-fork)

![](https://img1.dotnet9.com/2022/05/4906.gif)

2022-02-16:

层级关系,旋转

[https://lottiefiles.com/85410-wifi](https://lottiefiles.com/85410-wifi)

![](https://img1.dotnet9.com/2022/05/4907.gif)

2022-02-18

处理 PreCompLayer,LayerCollectionAsset

[https://lottiefiles.com/88964-snowman](https://lottiefiles.com/88964-snowman)

![](https://img1.dotnet9.com/2022/05/4908.gif)

更复杂的路径动画. 对比 Lottie Viewer 提示需要 UAP11,我已经升级到 Windows10 21H2 了 依然无法播放,不清楚具体原因.

[https://lottiefiles.com/87491-liquid-blobby-loader-green](https://lottiefiles.com/87491-liquid-blobby-loader-green)

![](https://img1.dotnet9.com/2022/05/4909.gif)

2022-02-19

[https://lottiefiles.com/93748-camera-roll](https://lottiefiles.com/93748-camera-roll)

![](https://img1.dotnet9.com/2022/05/4910.gif)

ImageAsset 和 ImageLayer

[https://lottiefiles.com/93795-dog-car-ride](https://lottiefiles.com/93795-dog-car-ride)

![](https://img1.dotnet9.com/2022/05/4911.gif)

Opacity

[https://lottiefiles.com/94910-greenish-arrow-down](https://lottiefiles.com/94910-greenish-arrow-down)

![](https://img1.dotnet9.com/2022/05/4912.gif)

2022-02-26

[https://lottiefiles.com/87670-satisfied-bear](https://lottiefiles.com/87670-satisfied-bear)

![](https://img1.dotnet9.com/2022/05/4913.gif)

[https://lottiefiles.com/96051-tta-certificate](https://lottiefiles.com/96051-tta-certificate)

![](https://img1.dotnet9.com/2022/05/4914.gif)

更复杂的例子

[https://lottiefiles.com/96388-tiger](https://lottiefiles.com/96388-tiger)

![](https://img1.dotnet9.com/2022/05/4915.gif)

2022-02-27

路径的 FillType

[https://lottiefiles.com/96511-listening-animation](https://lottiefiles.com/96511-listening-animation)

![](https://img1.dotnet9.com/2022/05/4916.gif)

2022-03-02

[https://lottiefiles.com/97409-404-page-animation](https://lottiefiles.com/97409-404-page-animation)

![](https://img1.dotnet9.com/2022/05/4917.gif)

蒙版 (+模式)

[https://lottiefiles.com/88967-food-delivery-service](https://lottiefiles.com/88967-food-delivery-service)

![](https://img1.dotnet9.com/2022/05/4918.gif)

蒙版(-模式)

[https://lottiefiles.com/83667-arty-chat](https://lottiefiles.com/83667-arty-chat)

![](https://img1.dotnet9.com/2022/05/4919.gif)

[https://lottiefiles.com/99804-backend-blue](https://lottiefiles.com/99804-backend-blue)

![](https://img1.dotnet9.com/2022/05/4920.gif)

遮罩(+模式)

[https://lottiefiles.com/96957-lock](https://lottiefiles.com/96957-lock)

![](https://img1.dotnet9.com/2022/05/4921.gif)

[https://lottiefiles.com/89881-smiling-star](https://lottiefiles.com/89881-smiling-star)

![](https://img1.dotnet9.com/2022/05/4922.gif)

2020-04-01

[https://lottiefiles.com/83395-addland-heatfav-v2](https://lottiefiles.com/83395-addland-heatfav-v2)

![](https://img1.dotnet9.com/2022/05/4923.gif)

[https://lottiefiles.com/96939-files](https://lottiefiles.com/96939-files)

![](https://img1.dotnet9.com/2022/05/4924.gif)

2022-04-17

[https://lottiefiles.com/96245-success](https://lottiefiles.com/96245-success)

![](https://img1.dotnet9.com/2022/05/4925.gif)

[https://lottiefiles.com/88282-rocket](https://lottiefiles.com/88282-rocket)

![](https://img1.dotnet9.com/2022/05/4926.gif)

[https://github.com/CommunityToolkit/Lottie-Windows/files/8182417/Lottie.Animation.zip](https://github.com/CommunityToolkit/Lottie-Windows/files/8182417/Lottie.Animation.zip)

![](https://img1.dotnet9.com/2022/05/4927.gif)

[https://lottiefiles.com/102516-growth](https://lottiefiles.com/102516-growth)

![](https://img1.dotnet9.com/2022/05/4928.gif)
