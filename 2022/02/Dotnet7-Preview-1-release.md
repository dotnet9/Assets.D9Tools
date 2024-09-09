---
title: .NET 7 预览版 1 发布
slug: Dotnet7-Preview-1-release
description: .NET 7 建立在 .NET 6 建立的基础之上，包括一套统一的基础库、运行时和 SDK，简化了开发体验，提高了开发人员的工作效率。
date: 2022-02-19 01:01:56
copyright: Reprinted
author: WeihanLi amazingdotnet
originalTitle: .NET 7 预览版 1 发布
originalLink: https://mp.weixin.qq.com/s/m7pl_JOerkKU8CqWIoDtRw
draft: False
cover: https://img1.dotnet9.com/2022/01/cover_02.png
categories: 
    - .NET
tags: 
    - .NET
---

今天，我们很高兴地宣布 .NET 历史上的下一个里程碑。[在庆祝社区和 20 年创新的同时](https://dotnet.microsoft.com/)，.NET 7 Preview 1 标志着迈向 .NET 下一个 20 年的第一步。

[ASP.NET Core Preview 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-7-preview-1) 和 [EF7 Preview 1](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-7-preview-1) 也在今天发布。

.NET 7 建立在 .NET 6 建立的基础之上，包括一套统一的基础库、运行时和 SDK，简化了开发体验，提高了开发人员的工作效率。.NET 7 的主要关注领域包括改进对云原生方案的支持、更容易升级旧项目的工具，以及通过更轻松地使用容器来简化开发人员体验。

.NET 7 Preview 1 包括对 API 的支持可空性的注解、持续的 JIT 编译器优化、新的 API 以及对更多热重载方案的支持。

.NET 版本包括产品、类库、运行时和工具，代表了 Microsoft 内外多个团队之间的协作。本篇博文中涵盖的更广泛的主题并未涵盖 .NET 7 的所有关键场景和投资。它们代表了很大的领域，但只是进入 .NET 7 中所有重要工作的一部分。我们计划对 ASP.NET Core、Blazor、EF Core、WinForms、WPF 和其他平台进行广泛投资，您可以通过阅读产品路线图了解关于这些领域的更多信息：

- [ASP.NET Core、Blazor 和 Orleans](https://github.com/dotnet/aspnetcore/issues/39504)
- [EF7](https://docs.microsoft.com/ef/core/what-is-new/ef-core-7.0/plan)
- [ML.NET](https://github.com/dotnet/machinelearning/blob/main/ROADMAP.md)
- [.NET MAUI](https://github.com/dotnet/maui/wiki/Roadmap)
- [WinForms](https://github.com/dotnet/winforms/blob/main/docs/roadmap.md)
- [WPF](https://github.com/dotnet/wpf/blob/main/roadmap.md)
- [NuGet](https://github.com/NuGet/Home/issues/11571)
- [Roslyn](https://github.com/dotnet/roslyn/blob/main/docs/Language Feature Status.md)

您可以下载适用于 Windows、macOS 和 Linux 的 [.NET 7 Preview 1](https://dotnet.microsoft.com/download/dotnet/7.0) 。

- [安装程序和二进制文件](https://dotnet.microsoft.com/download/dotnet/7.0)
- [容器镜像](https://hub.docker.com/_/microsoft-dotnet)
- [Linux 软件包](https://github.com/dotnet/core/blob/main/release-notes/7.0/linux-packages.md)
- [发行说明](https://github.com/dotnet/core/tree/master/release-notes/7.0)
- [已知的问题](https://github.com/dotnet/core/blob/main/release-notes/7.0/known-issues.md)
- [GitHub 问题跟踪器](https://github.com/dotnet/core/issues)

.NET 7 已通过 Visual Studio 17.2 [预览版 1 进行测试](https://visualstudio.com/preview)。如果您想在 Visual Studio 系列产品中试用 .NET 7，我们建议您使用预览频道版本。Visual Studio for Mac 对 .NET 7 预览版的支持尚不可用，但即将推出。

## 现代化客户端：.NET 多平台应用程序 UI (.NET MAUI)

.NET MAUI 是 .NET 跨平台原生 UI 的未来，将成为 .NET 7 的一部分。周二，我们发布了[.NET MAUI Preview 13](https://devblogs.microsoft.com/dotnet/announcing-net-maui-preview-13/)。我们目前专注于为 .NET 6 提供 .NET MAUI 支持，我们预计很快会提供一个候选版本 (RC)。在我们有可用的 RC 后，我们将专注于发布额外的 RC，直到我们达到一般可用性 (GA) 所需的稳定性。在 .NET MAUI GA 为 .NET 6 发布后，我们将把它包含在 .NET 7 中，并着眼于改进内部开发循环体验、支持最新的 .NET SDK 工具、更快的应用程序性能、共享更多代码以及增强的互操作等主题。查看[.NET MAUI 的状态](https://github.com/dotnet/maui/wiki/status)和[项目路线图](https://github.com/dotnet/maui/wiki/roadmap)以获取更多信息。

## 现代云：容器和云原生

从零开始构建云原生应用程序，利用基于 Web 的现代资源，例如[数据库服务](https://docs.microsoft.com/azure/?product=databases)和[托管容器](https://docs.microsoft.com/azure/?product=containers)。云原生架构可以通过创建独立于应用程序的其他区域进行部署和扩展的自治子系统（通常称为[微服务](https://docs.microsoft.com/dotnet/architecture/microservices/)）来提高大型应用程序的规模，同时降低长期成本。微服务架构是一种流行的方法，因为它很灵活，旨在发展和扩展至单体架构中难以实现的极限。

.NET 7 将通过探索对开发人员体验的改进来更轻松地构建云原生应用程序，例如：

- 简化实施安全身份验证和授权所需的设置和配置
- 提高应用程序启动和运行时执行的性能。

我们将继续投资[Orleans](https://dotnet.github.io/orleans/)，这是一个 .NET 跨平台框架，用于构建被称为 “分布式 .NET” 的分布式应用程序。我们将继续增强 [Orleans 的综合文档](https://docs.microsoft.com/dotnet/orleans/)，并通过改进 Orleans 与[Azure App Services](https://docs.microsoft.com/azure/app-service/)和[Azure Container Apps](https://docs.microsoft.com/azure/container-apps/overview)等现有云服务的集成，使其更易于使用和实施。

容器是当今许多公司部署云原生应用程序和微服务的首选方式。依赖容器带来了一些挑战，包括管理兼容性、构建和发布镜像、让镜像更安全以及简化镜像的大小和性能。我们相信有机会使用 .NET 容器创造更好的体验。

为了帮助客户应对这些挑战，我们计划使用 .NET 7 中的容器对 .NET 开发进行重大改进。例如，我们将探索通过 MSBuild 直接构建容器作为 SDK 的一项新功能。我们计划增强遥测以提高容器的可观察性。我们还将专注于使我们的容器镜像更小、更快、更安全，同时探索高要求的应用模型，例如 rootless 和 distroless。

## 现代化：升级 .NET 应用程序

自 .NET 6 发布以来，开发人员一直在升级他们的应用程序，以利用新的性能提升、最小 API 和热重载等生产力特性、新的运行时和 C# 语言创新以及成熟的库和工具生态系统的可用性。在 .NET 7 中，我们将继续使您能够将现有的 .NET 应用程序升级到最新的 .NET 平台和技术。[.NET 升级助手](https://dotnet.microsoft.com/platform/upgrade-assistant)中的更多分析器、代码修复程序和对其他应用程序类型的支持将帮助您自信地升级更多应用程序组合，并减少升级所涉及的重复性任务的时间。

我们还知道，每个 .NET 应用程序模型（ASP.NET、WinForms、WPF 等）在现代化方面都有其独特的挑战，并且可能缺少开发人员所需的功能或平台本身的支持。对于其中一些，例如 WCF，可能没有明确的方向。我们将专注于提供适当的指导、文档和工具，以使这些 .NET 应用程序模型更易于升级。

## 支持

.NET 7 是 Current 版本，这意味着它将在发布之日起 18 个月内获得免费支持和补丁。请务必注意，所有版本的质量都是相同的。LTS 和当前版本之间的唯一区别是支持的长度。有关 .NET 支持政策的更多信息，请参阅[.NET 和 .NET Core 官方支持政策](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

## 重大变化

您可以通过阅读 .NET 7 中的重大更改文档找到最新的 [.NET 7 重大更改](https://docs.microsoft.com/dotnet/core/compatibility/7.0)列表。它按区域和版本列出了重大更改，并附有详细说明的链接。

要查看提出了哪些重大更改但仍在审核中，请关注[Proposed .NET Breaking Changes GitHub 问题](https://github.com/dotnet/core/issues/7131)。

## 预览 1

Preview 1 版本现在提供以下功能。

### Microsoft.Extensions 的可空注解

我们在为 Microsoft.Extensions.\* 库添加注解以实现可空性方面取得了进展。在 .NET 7 Preview 1 中，以下库已针对可空性进行了注释：

- Microsoft.Extensions.DependencyInjection.Abstractions
- Microsoft.Extensions.Logging.Abstractions
- Microsoft.Extensions.Primitives
- Microsoft.Extensions.FileSystemGlobbing
- Microsoft.Extensions.DependencyModel
- Microsoft.Extensions.Configuration.Abstractions
- Microsoft.Extensions.FileProviders.Abstractions
- Microsoft.Extensions.FileProviders.Physical
- Microsoft.Extensions.Configuration
- Microsoft.Extensions.Configuration.Binder
- Microsoft.Extensions.Configuration.CommandLine
- Microsoft.Extensions.Configuration.EnvironmentVariables
- Microsoft.Extensions.Configuration.FileExtensions
- Microsoft.Extensions.Configuration.Ini
- Microsoft.Extensions.Configuration.Json

到 .NET 7 发布时，我们计划为所有 Microsoft.Extensions.\* 库添加可空性注释。您可以查看剩余的库，并在[dotnet/runtime#43605](https://github.com/dotnet/runtime/issues/43605)处关注进度。

非常感谢 @maxkoshevoi，他为这项工作做出了很大贡献。如果没有 @maxkoshevoi 的帮助，我们就不会走得那么远。

### 可观察性

继续改进跟踪 API：

- 添加 ActivityContext.TryParse 重载以允许解析和创建 ActivityContext 对象，包括是否从远程父级传播活动上下文（[相关问题](https://github.com/dotnet/runtime/issues/42575)）。
- 添加 Activity.IsStopped() 指示 Activity 对象是否停止的方法（[相关问题](https://github.com/dotnet/runtime/issues/63353)）。

### 代码生成

社区 PR（非常感谢 JIT 社区贡献者！！）

来自@am11

[将一些旧式内部函数转换为 NamedIntrinsic runtime#62271](https://github.com/dotnet/runtime/pull/62271)

来自@anthonycanino

[将额外的二进制操作添加到 RangeCheck 分析中。runtime#61662](https://github.com/dotnet/runtime/pull/61662)

来自@SeanWoo

[[JIT] [问题：61620] 针对 \*x = dblCns 优化 ARM64；runtime#61847](https://github.com/dotnet/runtime/pull/61847)

来自@SingleAccretion

在调用更好的运行时调整浮点 CSE runtime#63903
调整大小时更新新 CSE 的哈希 runtime#61984
重写字段选择并始终在 VN 规范化 SIMD 类型 runtime#61370
添加有关 VN 如何编号内存的文档 runtime#60476
改进强制转换数值 runtime#59841
在块 morphing 中的复杂本地地址下的地址暴露本地变量 runtime#63100
处理嵌入式分配时复制传播 runtime#63447
异常集：调试器检查和修复 runtime#63539
在 emitOutputAM 实现“moffset”编码大小优化 runtime#62896
压缩操作数种类数组并优化 OperIsLocal runtime#63253
使 gtHasRef 关注 LCL_FLD 节点 runtime#62568
为 GT_LCL_FLD 启用全局常量传播 runtime#61209
为小型类型启用全局常量传播 runtime#57726
fgMemoryVNForLoopSideEffects 属性类型主选择器 runtime#60505

来自@RalfKornmannEnvision

CoreRT 支持 ARM64&Unix runtime#41023

来自@weilinwa

基于覆写优化 FMA 代码生成 runtime#58196

动态 PGO
对 Arm64 的 OSR 支持
JIT：支持同步方法的 OSR
JIT：处理 OSR、PGO 和尾调用的交互
添加 2009 Jit 架构计划（节选）
JIT：一些 relops 的有限版本的前向替换
JIT：为后期去虚拟化保存泛型上下文
Arm64
Arm64：内存屏障改进
图片

在 InitBlkUnroll/CopyBlkUnroll 中使用 SIMD 操作并将展开限制增加到 128 字节
[Arm64] 继续展开 InitBlock 和 CopyBlock 最多 128 字节
'cmeq' 和 'fcmeq' Vector64.Zero/Vector128.Zero ARM64 包含优化
[arm64] JIT: X % 2 == 0 -> X & 1 == 0
[arm64] JIT：添加符号/零扩展
[arm64] JIT：为“arrayBase + elementOffset”启用 CSE/提升
[arm64] JIT：将“A \* B + C”折叠为 MADD/MSUB
循环优化
泛化循环前标头创建和循环提升
循环重构和注释改进
通用优化
加速其他跨平台硬件内在函数
使用 SIMDAsHWIntrinsic 实现 Narrow 和 Widen
添加 IsKnownConstant jit 助手并使用 str.StartsWith('c') 优化 'str == “”'
允许 JIT 在将 HFA/HVA 作为参数/返回值传递时将它们保留在寄存器中
为 Vector64/128/256 启用对 nint/nuint 的支持
添加对 X86Base.Pause() 和 ArmBase.Yield() 的支持
将 PAL 中的首选区域用于 JIT reloc 提示
支持 R2R 中的快速尾调用
允许在 x64 上的尾调用中包含间接寻址
更一般地优化间接单元调用序列
避免为委托调用创建额外的本地
互操作：p/Invoke 代码生成
我们将在 .NET 6 中原型化的 p/invoke 源生成器(Source Generator) 集成到 dotnet/runtime 中，并且一直在转换运行时库以使用它。这意味着转换后的 p/invokes 与 AOT 兼容，不再需要在运行时生成 IL Stub。

我们打算在未来使 p/invoke 源生成器在运行时之外可用。您可以在 dotnet/runtime#60595 中关注我们剩余的工作。

System.Text.Json 中的新 API
System.Text.Json 附带了一些小的生命周期增强功能：

开发人员现在可以访问 System.Text.Json 内部使用的默认单例 JsonSerializerOptions（相关问题）。
添加一个 JsonWriterOptions.MaxDepth 属性并确保此值来自序列化的 JsonSerializerOptions.MaxDepth 等效属性（相关问题）。
将 Patch 方法添加到 System.Net.Http.Json（相关问题）。
热重载改进
现在允许在 C# 热重载中为 Blazor WebAssembly 和 .NET 为 iOS 和 Android 进行以下编辑（相关问题）：

向现有方法添加静态 lambda
将捕获这个的 lambda 添加到已经有至少一个捕获这个的 lambda 的现有方法中
向现有类添加新的静态或非虚拟实例方法
向现有类添加新的静态字段
添加新类
已知的问题：

不支持新增类中的实例字段
现有或新类中新添加的方法和字段对反射不可见
您可以在 dotnet/runtime#57365 中关注我们的进度

指定 .NET 7
要使用 .NET 7，您需要在项目文件中使用 .NET 7 Target Framework Moniker (TFM)。例如：

<TargetFramework>net7.0</TargetFramework>
全套 .NET 7 TFM，包括特定于操作系统的 TFM。

net7.0
net7.0-android
net7.0-ios
net7.0-maccatalyst
net7.0-macos
net7.0-tvos
net7.0-windows
我们希望从 .NET 6 升级到 .NET 7 应该很简单。请报告您在使用 .NET 7 测试现有应用程序的过程中发现的任何重大更改。

结语
Microsoft 的全球多元化工程师团队与高度参与的开发人员社区合作，正在构建 .NET 7。广泛的 .NET 社区，包括从学生和爱好者到开源贡献者和企业客户的每个人，是 .NET 的核心，他们提出新想法，定期贡献代码，并推动 .NET 生态系统向前发展。我们感激并感谢您的支持、贡献和见解。

欢迎使用 .NET 7。
