---
title: Flutter 3 发布了(文末推荐一个免费的在线Flutter学习教程)
slug: Introducing-Flutter-3
description: Flutter 3发布了，你还在等什么呢？一起来了解、学习吧！！！
date: 2022-05-12 08:25:17
copyright: Original
originalTitle: Flutter 3 发布了(文末推荐一个免费的在线Flutter学习教程)
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_35.png
categories: 
    - 前端
tags: 
    - Flutter
---

> 翻译自 [Tim Sneath](https://medium.com/@timsneath) 2022 年 5 月 12 日的文章 [《Introducing Flutter 3》](https://medium.com/flutter/introducing-flutter-3-5eb69151622f)

---

> 作者 ： [Tim Sneath](https://medium.com/@timsneath)
>
> 翻译 ： 沙漠尽头的狼(谷歌翻译加持)
>
> 链接 ： [Introducing Flutter 3](https://medium.com/flutter/introducing-flutter-3-5eb69151622f)(英文原文)

我们在手机、桌面和网站开发上进行多平台 UI 开发的历程达到了顶峰。

我们很高兴地宣布，作为 Google I/O 主题演讲的一部分推出 Flutter 3。Flutter 3 完成了我们从以移动为中心到多平台框架的路线图，提供了对 macOS 和 Linux 桌面应用的支持，以及对 Firebase 集成的改进、新的生产力和性能特性以及对 Apple Silicon 的支持。

![](https://img1.dotnet9.com/2022/05/3501.png)

## Flutter 3 之旅

我们创建 Flutter 是为了彻底改变应用程序开发：将 Web 的迭代开发模式与硬件加速的图形渲染和像素级控制相结合，这些都是以前游戏的专利。自 Flutter 1.0 beta 发布以来的过去四年里，我们逐渐在这些基础上构建，添加了新的框架功能和新的小部件，与底层平台更深入的集成，丰富的包(库）以及许多性能和工具改进。

![](https://img1.dotnet9.com/2022/05/3502.png)

随着产品的成熟，越来越多的人开始使用它构建应用程序。 如今，有超过 500,000 个使用 Flutter 构建的应用程序。 来自 [data.ai](https://www.data.ai/en/) 等研究公司的分析以及公开推荐表明，Flutter 被[许多细分市场的广泛客户](https://flutter.dev/showcase)使用：从[微信](https://play.google.com/store/apps/details?id=com.tencent.mm&hl=en_US&gl=US)等社交应用程序到 [Betterment](https://apps.apple.com/us/app/betterment-investing-saving/id393156562) 和 [Nubank](https://play.google.com/store/apps/details?id=com.nu.production&hl=en_US&gl=US) 等金融和银行应用程序； 从 [SHEIN](https://play.google.com/store/apps/details?id=com.zzkko&hl=en_US&gl=US) 和 [trip.com](https://apps.apple.com/us/app/trip-com-hotels-flights-trains/id681752345) 等商务应用到 [Fastic](https://fastic.com/) 和 [Tabcorp](https://auspreneur.com.au/tabcorp-adopts-googles-flutter-platform/) 等生活方式应用； 从 [My BMW](https://www.press.bmwgroup.com/global/article/detail/T0328610EN/the-my-bmw-app:-new-features-and-tech-insights-for-march-2021?language=en) 等配套应用程序到[巴西政府](https://apps.apple.com/app/id1506827551)等公共机构。

> 今天，有超过 50 万个应用程序使用 Flutter 构建。

**站长注：这里有个视频 https://youtu.be/8RmsstcNE1Y**

![](https://img1.dotnet9.com/2022/05/3503.png)

开发人员告诉我们，Flutter 有助于更快地为更多平台构建漂亮的应用程序。 在我们最近的用户研究中：

- 91% 的开发人员认同 Flutter 减少了构建和发布应用程序所需的时间。
- 85% 的开发者认同 Flutter 让他们的应用比以前更漂亮。
- 85% 的开发者认同这使他们能够为比以前更多的平台发布他们的应用程序。

在 [Sonos 最近的一篇博客文章](https://tech-blog.sonos.com/posts/renovating-setup-with-flutter/)中讨论了他们改进的设置体验，他们强调了其中的第二个：

> “毫不夸张地说，[Flutter] 解锁了一定程度的“溢价”，这与我们团队以前提供的任何东西都不同。 对我们的设计师来说最重要的是，可以轻松构建新的 UI，这意味着我们的团队花更少的时间对规范说“不”，而将更多的时间用于迭代规范。 如果这听起来值得，我们建议尝试一下 Flutter——我们很高兴我们做到了。”

## 介绍 Flutter 3

今天，我们将介绍 Flutter 3，这是我们填补 Flutter 支持的平台之旅的高潮。 使用 Flutter 3，您可以从一个代码库为六个平台构建美妙的体验，为开发人员提供无与伦比的生产力，并使初创公司从一开始就将新想法带入完整的目标市场。

在之前的版本中，我们通过 [Web](https://medium.com/flutter/flutter-web-support-hits-the-stable-milestone-d6b84e83b425) 和 [Windows 支持](https://medium.com/flutter/announcing-flutter-for-windows-6979d0d01fed)补充了 iOS 和 Android，现在 **Flutter 3 增加了对 macOS 和 Linux 应用程序的稳定支持**。 添加平台支持需要的不仅仅是渲染像素：它包括新的输入和交互模型、编译和构建支持、可访问性和国际化以及特定于平台的集成。 我们的目标是让您能够灵活地充分利用底层操作系统，同时根据您的选择共享尽可能多的 UI 和逻辑。

在 macOS 上，我们投入支持 Intel 和 Apple Silicon，提供[通用二进制](https://developer.apple.com/documentation/apple-silicon/building-a-universal-macos-binary)支持，允许应用打包在两种架构上本地运行的可执行文件。 在 Linux 上，Canonical 和 Google 合作提供了一个高度集成的、同类最佳的开发选项。

[Superlist](https://superlist.com/) 是 Flutter 如何实现漂亮桌面体验的一个很好的例子，它今天推出了测试版。 Superlist 通过将列表、任务和自由形式的内容组合成全新的待办事项列表和个人计划的新应用程序，提供超强协作。 Superlist 团队之所以选择 Flutter，是因为它能够提供快速、高度品牌化的桌面体验，我们认为他们迄今为止的进展证明了为什么它已被证明是一个不错的选择。

**站长注：这里有个视频 https://youtu.be/YRuQj7mlH2I**

![](https://img1.dotnet9.com/2022/05/3504.png)

Flutter 3 还对许多基本要素进行了改进，提高了性能，支持 Material You，并更新了生产力。

除了上面提到的工作，在这个版本中，Flutter 可以完全原生的在 [Apple 芯片](https://support.apple.com/en-us/HT211814)上进行开发。 虽然 Flutter 自发布以来一直与 M1 驱动的 Apple 设备兼容，但 Flutter 现在充分利用了 [Dart 对 Apple 芯片的支持](https://medium.com/dartlang/announcing-dart-2-14-b48b9bb2fb67)，从而可以在 M1 驱动的设备上更快地编译并支持 macOS 应用程序的[通用二进制](https://developer.apple.com/documentation/apple-silicon/building-a-universal-macos-binary)文件。

我们对 [Material Design 3](https://m3.material.io/) 的工作在此版本中基本完成，使开发者能够利用一个适应性强、跨平台的设计系统，提供动态的色彩方案和更新的视觉组件。

![](https://img1.dotnet9.com/2022/05/3505.png)

我们详细的技术博客文章扩展了 Flutter 3 中的这些以及许多其他新功能。

Flutter 由 Dart 提供支持，Dart 是一种用于多平台开发的高生产力、可移植语言。 我们在这个周期中对 Dart 的工作包括减少模板和提高可读性的新语言功能、实验性 RISC-V 支持、升级的 linter 和新文档。 有关 Dart 2.17 中所有新改进的更多详细信息，请查看[专用博客](https://medium.com/dartlang)。

## Firebase and Flutter

当然，构建应用程序不仅仅是 UI 框架。应用程序发布者需要一整套工具来帮助您构建、发布和操作您的应用程序，包括身份验证、数据存储、云功能和设备测试等服务。有多种服务支持 Flutter，包括 [Sentry](https://docs.sentry.io/platforms/flutter/)、[AppWrite](https://appwrite.io/docs/getting-started-for-flutter) 和 [AWS Amplify](https://docs.amplify.aws/start/q/integration/flutter/)。

Google 提供的应用服务是 Firebase，[SlashData 的开发者基准测试研究](https://www.slashdata.co/developer-program-benchmarking/?)表明，62% 的 Flutter 开发者在他们的应用中使用 Firebase。因此，在过去的几个版本中，我们一直在与 Firebase 合作，以扩展和更好地将 Flutter 集成为一流的集成。这包括将 Flutter 的 Firebase 插件引入 1.0，添加更好的文档和工具，以及[ FlutterFire UI](https://pub.dev/packages/flutterfire_ui) 等新小部件，为开发人员提供可重用的身份验证和配置文件界面 UI。

今天，我们宣布 Flutter/Firebase 集成升级为 Firebase 产品的完全支持的核心部分。我们正在将源代码和文档移动到主要的 Firebase 存储库和站点中，您可以指望我们与 Android 和 iOS 同步发展 Firebase 对 Flutter 的支持。

此外，我们还进行了重大改进，以支持使用 Crashlytics 的 Flutter 应用程序， 这是 Firebase 流行的实时崩溃报告服务 。通过 [Flutter Crashlytics 插件](https://firebase.google.com/docs/crashlytics)的更新，您可以实时跟踪致命错误，为您提供与其他 iOS 和 Android 开发人员相同的功能集。这包括重要的警报和指标，如“无崩溃用户”，可帮助您掌握应用程序的稳定性。 Crashlytics 分析管道已升级以改进 Flutter 崩溃的聚类，从而更快地对问题进行分类、优先排序和修复问题。最后，我们简化了插件设置过程，因此只需几个步骤即可从您的 Dart 代码中启动和运行 Crashlytics。

## Flutter 休闲游戏工具包(Flutter Casual Games Toolkit)

对于大多数开发者来说，Flutter 是一个应用程序框架。 但是，围绕休闲游戏开发的社区也在不断壮大，利用 Flutter 提供的硬件加速图形支持以及 [Flame](https://flame-engine.org/) 等开源游戏引擎。 我们希望让休闲游戏开发者更容易上手，因此在今天的 I/O 大会上，我们宣布推出[休闲游戏工具包](https://flutter.dev/games)，它提供了模板和最佳实践的入门工具包以及广告和云服务的良好体验。

![](https://img1.dotnet9.com/2022/05/3506.png)

尽管 Flutter 并非专为高强度 3D 动作游戏而设计，但即使其中一些游戏已经转向 Flutter 的非游戏 UI，包括拥有数亿用户的热门游戏，如 [PUBG Mobile](https://play.google.com/store/apps/details?id=com.tencent.ig)。 对于 I/O，我们想看看我们可以将我们的技术推到多远，所以我们创建了一个有趣的弹球游戏，由 Firebase 和 Flutter web 提供支持。 I/O Pinball 提供了一个围绕 Google 最喜欢的四个吉祥物设计的自定义桌子：Flutter 的 Dash、Firebase 的 Sparky、Android 机器人和 Chrome 恐龙，并让您与其他人竞争高分。 我们认为这是展示 Flutter 多功能性的一种有趣方式。

![](https://img1.dotnet9.com/2022/05/3507.png)

## 由 Google 赞助，由社区提供支持

我们喜欢 Flutter 的一件事是，它不仅仅是一款 Google 产品——它是一款“所有人”的产品。 开源意味着我们都可以参与并受益于它的成功，无论是通过贡献新代码或文档，创建赋予核心框架新的超级大国的软件包，编写书籍和培训课程来教授他人，或者帮助组织活动和用户组 .

为了展示社区的最佳状态，我们最近与 DevPost 合作赞助了 Puzzle Hack 挑战赛，让开发人员有机会通过使用 Flutter 重新构想经典的滑动拼图来展示他们的技能。 这证明了 web、桌面和移动如何结合在一起的精彩演示：现在我们都可以在线或通过商店玩游戏。

**站长注：这里有个视频 https://youtu.be/l6hw4o6_Wcs**

![](https://img1.dotnet9.com/2022/05/3508.png)

谢谢您对 Flutter 的支持，欢迎来到 Flutter 3!

## 结尾福利

这是站长收藏的一个在线学习 Flutter 的网站，希望对大家有用：

- [《Flutter 实战·第二版》](https://book.flutterchina.club/)

![](https://img1.dotnet9.com/2022/05/3509.gif)
