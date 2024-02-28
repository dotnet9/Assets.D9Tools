---
title: Flutter稳定支持Windows，开发者做好准备了吗？
slug: Flutter-supports-windows-stably-Are-developers-ready
description: Flutter是由谷歌开发的开源移动UI框架，可快速在不同平台上构建高质量原生用户界面。
date: 2022-02-07 22:24:11
copyright: Reprinted
author: 技术视野
originaltitle: Flutter稳定支持Windows，开发者做好准备了吗？
originallink: https://blog.csdn.net/csdndevelopers/article/details/122808516
draft: False
cover: https://img1.dotnet9.com/2022/02/0707.png
categories: 前端
tags: Flutter
---

Flutter是由谷歌开发的开源移动UI框架，可快速在不同平台上构建高质量原生用户界面。Flutter支持现有的所有代码，在世界各地受到越来越多开发者的追捧。到目前为止，全球已发布了近50万个使用Flutter的应用程序，其中包括来自字节跳动等大型公司的应用程序，以及谷歌三十个团队的应用程序。据Statista和SlashData的分析师表示，Flutter是2021年最受欢迎的跨平台UI工具。

>整理 | 郭露
>
>出品 | CSDN（ID：CSDNnews）

2月4日，Flutter稳定版2.10重磅推出，该版本在春节期间发布，距离上次的发布还不到两个月。但即使在这么短的时间内，我们已经关闭了1843个issues以及来自全球115名贡献者的1525个PR。此次版本更新包括Flutter对Windows的重大支持更新、部分性能改进、不同平台的新增功能以及一些工具的改进。

![图源自Flutter](https://img1.dotnet9.com/2022/02/0701.png)

## Flutter 2.10 新特性一览

### 1. 性能改进

Flutter 2.10版本初步支持由Flutter社区成员knopp提供的绘制脏区管理功能。他为 iOS/Metal上的单个脏区域启用了选择性重绘。这将大大缩短部分基准测试中的九十分位和九十九分位的光栅化时间，并将这些基准测试中的GPU占用率从90%以上降低到10%以下。

除此之外，该版本还对图片格式进行了优化。开发者可以更有效地调节图层透明度。基准测试中每帧光栅化时间至少缩短了三分之一。

在profile和release模式下，Dart代码以AOT方式进行编译。这段代码解锁了许多编译器优化和激进的tree-shaking。但是由于类型流分析必须涵盖整个程序，因此对性能有些影响。2.10版本带来了更快的类型流分析实现。在基准测试中，Flutter应用程序的总体构建时间缩短了约10%。

### 2. iOS更新

除了性能改进之外，Flutter 2.10版本还在各平台增加了不同的增强功能。例如开发者luckysmg带来了流畅的iOS键盘动画、iOS相机插件的稳定性提高，以及为64位架构的iOS系统加入了减少内存使用的新功能——压缩指针。

### 3. Android更新

同时Flutter 2.10版本针对Android进行了改进。当开发者创建新应用时，Flutter可支持最新版本的Android，即Android 12。此外，在此版本中，我们启用了MultiDex。如果开发者使用的是低于21的Android SDK版本，并超过了 64K方法数限制，只需将 --multidex 传递给flutter build appbundle 或是flutter build apk ，就能够支持MultiDex。

### 4. Web更新

Flutter版本中同样包括对于Web的改进。在之前的版本中，当鼠标拖动到多行文本框的边缘时，它不会同步滚动。而在2.10版本中，选择光标拖出了文本框后，文本框会进行滚动，浏览并选中对应的内容。该功能适用于Wed和桌面应用程序。

### 5. Material 3

Flutter 2.10是向Material 3过渡的开始，其中包括从单一颜色生成成整个配色方案的功能。

开发者可以使用任意颜色创建新的ColorScheme类型。ThemeData 的构造函数还包含一个新的 colorSchemeSeed参数，可以直接从颜色生成主题。此外，该版本包括了 ThemeData.useMaterial3 的参数，可将Widget切换到新的Material 3外观。
Flutter还增加了1028个新Material图标。

![图源自Flutter](https://img1.dotnet9.com/2022/02/0702.png)

除上述功能以外，Flutter 2.10还在集成测试、DevTools以及VSCode等进行了改进，并彻底移除了Dev渠道以及对 iOS 9.3.6支持。

对于此次版本更新，最引人关注的莫过于稳定支持Windows应用开发，对此，Flutter产品经理蒂姆·斯内斯（Tim Sneath）发文进行了详细解读，让我们一起来看一下。

## 已为 Windows 做好准备的 Flutter

![图源自Flutter官网](https://img1.dotnet9.com/2022/02/0703.png)

Flutter旨在创建高效跨平台软件框架，在过去几年取得了长足发展。Flutter可为 Android、iOS、Linux、Windows、macOS 以及网页开发应用，并兼容现有的所有代码。受到了全球各地区开发者的支持信赖。

谷歌表示：“今天标志着这一愿景的重大扩展，首次发布了对Windows作为应用目标的支持，使Windows开发者能够受益于移动开发者一直享有的同样的生产力和力量”。

为实现这一目标，谷歌一直致力于Flutter开发。五年前，谷歌曾推出Flutter Alpha，该版本大大提高了移动操作系统的开发速度。Flutter应用程序可使用Dart编写，实现了在Android、iOS、Windows、macOS和Linux、Web以及嵌入式设备上运行。

然而， 要想实现Flutter桌面支持并非易事，必须重新针对Windows进行设计，桌面应用需要兼容键盘和鼠标等不同硬件以及不同大小的屏幕，同时对于输入法、视觉样式等也有不同的需求，还要支持文件系统选择器以及Windows注册表等数据存储的各种内容。

正如Flutter对Android和iOS的支持一样，Flutter的Windows结合了Dart框架以及C++ 引擎。Windows和Flutter 通过嵌入层进行通信，该嵌入层承载Flutter引擎并负责翻译和分发Windows信息。Flutter与Windows可将UI绘制到屏幕上，并与现有的 Windows模式相配合。

![图源来自Flutter和Dart产品经理博客](https://img1.dotnet9.com/2022/02/0704.png)

开发者可在Windows上使用Flutter框架的所有功能，并通过Dart或C++ 编写的平台插件与Win32、COM和Windows Runtime API进行通信，同时Flutter团队还对许多常用插件进行调整以支持Windows，其中包括camera、file_picker以及shared_preferences 。更重要的是，Flutter社区中还添加了大量对其他包的Windows支持，其中涵盖了从Windows 任务栏集成到串行端口访问的所有内容 。

已有数百个包支持为Windows构建的Flutter应用程序

![图源来自Flutter](https://img1.dotnet9.com/2022/02/0704.png)

对于完全定制的 Windows UI，您还可以使用包fluent_ui来flutter_acrylic创建一个可以精美地表达 Microsoft Fluent 设计系统的应用程序。使用该msix工具，您可以将您的应用程序包装在一个安装程序中，该安装程序可以上传到 Windows 上的 Microsoft Store。

总的来说，Flutter 2.10的推出实现了在Windows上的快速运行，并且可以传输到其他桌面或移动设备以及Web。以下是早期示例：

![图源来自Flutter](https://img1.dotnet9.com/2022/02/0706.png)

![图源来自Flutter](https://img1.dotnet9.com/2022/02/0707.png)

该版本推出后，微软Windows开发者平台公司副总裁Kevin Gallo表示：”我们很高兴看到Flutter实现对创建Windows应用程序的支持。作为一个开放平台，Windows欢迎所有开发人员的加入。Flutter能实现Windows应用支持并在Microsoft Store上架，表明其对我们的信任，期待Flutter在Windows上的发展！”

除此之外，许多Flutter合作伙伴也在增加对Windows的支持，其中包括：

作为低代码Flutter应用程序设计工具，FlutterFlow宣布支持Windows并将帮助Flutter开发人员构建专为桌面使用的功能。

本地数据存储工具Realm的最新版本支持使用Flutter构建Windows应用程序，使用Dart FFI快速访问底层数据库，并增加了对 iOS 和Android等移动平台的现有支持。

Nevercode已更新Codemagic CI/CD 工具以支持Windows，使用户能够在云中测试并构建Windows应用程序，并自动部署到Microsoft Store。

Syncfusion已更新小部件支持Windows。数据可视化组件等支持创建PDF文件和 Excel表格。

Rive宣布了其图形工具套件即将推出Windows版本，开发人员可创建可以使用状态机实时响应代码的交互式矢量动画。他们还将推出Windows应用程序，在性能和内存上有着不俗的表现，很快将在Microsoft Store中提供下载。

目前看来，大家对于Flutter 2.10的评价依旧非常好。你对于Flutter 2.10的发布有什么期待呢？欢迎在下方留言。

**【参考资料】**

- [https://www.theregister.com/2022/02/04/flutter_windows_production_release/](https://www.theregister.com/2022/02/04/flutter_windows_production_release/)
- [https://medium.com/flutter/announcing-flutter-for-windows-6979d0d01fed](https://medium.com/flutter/announcing-flutter-for-windows-6979d0d01fed)
- [https://www.mongodb.com/developer/article/introducing-realm-flutter-sdk/](https://www.mongodb.com/developer/article/introducing-realm-flutter-sdk/)
- [https://medium.com/flutter/whats-new-in-flutter-2-10-5aafb0314b12](https://medium.com/flutter/whats-new-in-flutter-2-10-5aafb0314b12)

>版权声明：本文为CSDN博主「技术视野」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
>
>原文链接：https://blog.csdn.net/csdndevelopers/article/details/122808516