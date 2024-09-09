---
title: 【链接很多】.NET 7 的 ASP.NET Core 路线图
slug: many-links-ASP-Net-Core-of-Net-core-Roadmap
description: 我们计划在 .NET 7 版本中添加到服务器的一些功能亮点
date: 2022-02-19 18:05:34
copyright: Reprinted
author: 追逐时光者
originalTitle: 【链接很多】.NET 7 的 ASP.NET Core 路线图
originalLink: https://zhuanlan.zhihu.com/p/469359820
draft: False
cover: https://img1.dotnet9.com/2022/02/cover_05.png
categories: .NET
tags: 
    - .NET
    - ASP.NET Core
---

文章来源：[https://github.com/dotnet/aspnetcore/issues/39504](https://github.com/dotnet/aspnetcore/issues/39504)

转载自知乎：[https://zhuanlan.zhihu.com/p/469359820](https://zhuanlan.zhihu.com/p/469359820)

每个列表就是一个链接，大家看个大概，感兴趣点击上面的原文或者网站同步文章链接，公众号就不贴链接了，贴了也点击不了...

## ASP.NET Core 运行时

### 服务器 - 功能

以下是我们计划在 .NET 7 版本中添加到服务器的一些功能亮点：

- 添加对输出缓存的支持 #27387
- 使用 Kestrel+YARP 共享端口 #39640
- 用于 ARM64 的 ANCM #39281
- Kestrel 通过 HTTP/2 支持 WebSockets #7801
- OCSP 装订支持 Kestrel #4762
- Kestrel 的速率限制 - 将背压应用于接受连接的设计机制 #13295
- [主题] 支持将 .NET Framework 应用迁移到 .NET Core #40078

### 服务器 - 性能

.NET 6 是服务器性能的一个重要版本，我们希望在 .NET 7 中进一步改进，如下所示：

- 连接性能改进 #39846
- 允许服务器内存池收缩 #27394
- 为 HTTP.sys 服务器启用内核响应缓冲 #14455
- 使用 Channels #30235 提高 HTTP/2 性能

### HTTP/3 支持

HTTP/3 支持作为 .NET 6 中的预览功能提供。对于 .NET 7，我们希望完成它并使其成为受支持的功能。

- HTTP/3：UseHttps 配置 #34858
- 实现 Http3 静态响应头压缩 #33980
- 对 HTTP/3 的动态表支持 #28433
- HTTP/3：删除非最终 h3 ALPN #35032
- HTTP/3：在 .NET 7 中默认启用 Kestrel #36486

## ASP.NET Web 框架

### Minimal APIs

- 通过分组#36007 改进最小 api 路由
- 添加对端点过滤器的支持 #37853
- [史诗] .NET 7 中的 OpenAPI #37098
- 扩展为 Minimal API 自定义参数绑定的能力 #35489
- 向最小端点添加防伪 (anti-csrf) 支持 #38630
- 为各个端点/操作生成安全方案 #39761
- 支持绑定请求体为 Stream/ReadOnlySequence&lt;byte&gt; 或者 ReadOnlyMemory&lt;byte&gt;/ReadOnlySpan&lt;byte&gt; #38153
- 考虑一个更好的 API 来配置 JsonOptions 以实现最少的操作/路由到代码 #39226
- 使 IResult 方法更具可测试 性 #37502
- Minimal API 不支持从查询或标头绑定到 StringValues #36726
- 最小操作 - 来自查询字符串绑定的方法签名中的数组支持 #32516
- ProblemDetails 的使用在整个 ASP.NET Core #32957 中不一致
- 为授权配置最少的 API 应该非常简单和特色 #34545
- 最小 API 的设计验证 #30666
- [Epic]：Minimal APIs + MVC #36637 分析器（改善开发人员体验）

### SignalR

- 支持从客户端调用返回值 #5280
- 刷新 SignalR 的身份验证令牌 #5297
- [Epic] SignalR 应该有一个 C++ 客户端 #5301（改进现有的 C++ 客户端）

### Rate Limiting

- 完成非通用速率限制器的实现（固定窗口、滑动窗口、令牌桶、并发限制器） #37381
- 设计通用速率限制 API #37383
- 在 aspnetcore 中实现速率限制中间件 #37384
- 重新设计当前的 aspnetcore 并发限制器中间件以使用新的抽象和实现 #38306
- Yarp 实现的速率限制 #37387
- Kestrel 的速率限制 - 将背压应用于接受连接的设计机制 #13295
- 将速率限制抽象/API 移动到 dotnet/runtime #37385

### gRPC (dotnet-gRPC)

- [EPIC] gRPC JSON 转码 grpc/grpc-dotnet#1583 (gRPC/HTTP)

### Orleans

- 实施 POCO Grains orleans#7351
- 谷物持久性的可配置序列化程序 orleans#7471
- 删除 SMS 提供程序 orleans#7475
- 实现无状态服务抽象 orleans#7469
- [史诗]：在 Orleans 中将工作流实现为原语 orleans#7487
- [史诗]：改善奥尔良客户部署到 Azure orleans#7486 的体验
- 简化流配置抽象 orleans#7473
- 为 aspnetcore orleans#7479 添加 Orleans 模板

## ASP.NET Web UI

### Blazor Hybrid

- 带有 maui 的 Blazor 不能使用 &lt;InputSelect&gt; 或 &lt;select&gt; maui#4325
- Blazor 桌面原则：全球准备 maui#2532
- Blazor Desktop 测试基础架构和计划 maui#2543
- 支持打开外部 URL 的可配置性 maui#4338
- MAUI Blazor 应用程序上的热重载不起作用。 毛伊岛#2527
- Blazor 桌面：支持 auth maui#2529
- Blazor 桌面原则：安全 maui#2540
- Blazor 桌面原则：性能 maui#2531
- Blazor 桌面原则：可访问性 maui#2541
- Blazor 桌面原则：DPI 意识 maui#2549
- Blazor 桌面开发工具启用 maui#2542
- 文档：Blazor 混合演练/入门 maui#3684
- Blazor 混合项目模板更新 maui#4127
- [Blazor webview] blazor webview maui#4441 的异常处理

### Razor Compiler

- 当类型具有附加约束时，通用 Blazor 组件无法正确推断其类型 #25588
- 更新 Razor 编译器以更自由地使用 global:: #18757
- 通过内部遥测报告的编译器崩溃问题 #38877
- 通过内部遥测报告的编译器崩溃问题 #38878
- 通过内部遥测报告的编译器崩溃问题 #38879
- [Blazor] CascadingTypeParameter 问题 #38479
- 为 5.0 应用添加 SDK 快照测试 #28489
- 在代码块上方有一个未闭合的表单会使所有内容变为蓝色 #27133
- 远离 Razor 在设计时/运行时生成不同的代码。 #30669
- Razor 编辑器抱怨标签未关闭 #30573
- 在 Razor 语法树中定位所有者可能会失败 #36689

### Blazor

- 能够在同一文档中运行多个 Blazor 服务器/Web 程序集应用程序 #38128
- 为 NavigationManger 公开位置更改事件 #14962
- 调用 InvokeAsync(StateHasChanged) 会导致页面回退到默认区域性 #28521
- 执行清理 Web 模板的过程 #30162
- 空 Blazor 项目模板 #32832
- 考虑运送 ObjectGraphDataAnnotationsValidator / ValidateComplexTypeAttribute #28640
- 产品化自定义元素支持包 #38447
- 改进 Blazor 中的预呈现和身份验证体验 #27592
- 暂停和恢复 Blazor 应用程序 #27576
- [Blazor] 用于显示无法通过 HTTP 端点访问的图像的图像元素 #25274
- 设计方案：绑定 get/set/after 修饰符 #39837
- 用于解决 Blazor 服务器上的瞬态一次性用品的设计 #26676
- [Blazor] 暂停和恢复服务器端 Blazor 电路 #30344
- 处理大型二进制数据（文件和媒体）的特定组件 #30290
- 启用与 3rd 方资产管理解决方案的集成 #38445
- 组件在热重载更新期间不会重置其接收到的参数 #31272
- 为模型绑定和路由添加 DateOnly 和 TimeOnly 支持 #34591
- 对其他方案更好的 MSAL 支持 #38122
- 如何使用 IHttpClientFactory #25758 在 Blazor Server 中配置 HttpClient 基地址
- 对电路寿命的更多控制（监控电路活动并从客户端/服务器终止的能力） #30287
- 服务器端 Blazor：为 Circuit Eviction #17866 提供 API/扩展点
- 真正的多线程（在支持的浏览器上） #17730
- 针对 browser-wasm 的开发人员可以使用 Web Crypto APIs runtime#40074
- 继续改进 Mono 方法体替换 运行时#57365

### MVC

- EndpointRouting #23041 中的 IRouter.GetVirtualPath 替代方案
- 允许匹配路由而不捕获参数 #27996
- 十进制字段的本地化数字格式和错误的输入类型 #6566
- 链接生成改进 #38121
- LinkTagHelper 不应使用内联脚本 #14736
- 添加对 FromServices 出现在 DI #39667 中的类型的隐式推断的支持
- 推断 FromBody.AllowEmptyBehavior = Allow 基于可空性信息 #39754
- 考虑添加对 TryParse 作为绑定原语的方式的支持 #39682
- EmptyBodyBehavior.Allow 应该允许缺少 Content-Type #36466
- SkipStatusCodePagesAttribute 应该在 AuthorizeAttribute #10317 之前运行
- ApiExplorer 未正确处理同一操作方法上的多个路由 #26234
- FromServices 根据可空性信息推断可选性 # 39757
- Razor Pages ComplexTypeModelBinder 无法处理“或者，在 'YModel' 构造函数中将 'X' 属性设置为非空值” #21916 的建议
