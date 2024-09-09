---
title: CacheManager - 用 C# 编写的 .NET 的开源缓存抽象层
slug: CacheManager-Open-source-caching-abstraction-layer-for-odtNET-written-in-Csharp
description: CacheManager 是用 C# 编写的 .NET 的开源缓存抽象层。它支持各种缓存提供程序并实现了许多高级功能
date: 2022-07-08 07:03:43
copyright: Reprinted
author: 黑哥聊dotNet
originalTitle: CacheManager - 用 C# 编写的 .NET 的开源缓存抽象层
originalLink: https://mp.weixin.qq.com/s/k5u-JpLOUsjh__8mZTDNww
draft: False
cover: https://img1.dotnet9.com/2022/07/cover_09.png
categories: 
    - .NET
tags: 
    - .NET
---

## 简介

`CacheManager` 是用 C# 编写的 .NET 的`开源缓存抽象层`。它支持各种缓存提供程序并实现了许多高级功能。

`CacheManager` 包的主要目标是让开发人员的生活更容易处理，即使是非常复杂的缓存场景。使用 `CacheManager` 可以实现多层缓存，例如在分布式缓存前的进程内缓存，只需几行代码。

`CacheManager` 不仅仅是一个统一各种缓存提供者的编程模型的接口，这将使以后在项目中更改缓存策略变得非常容易。它还提供其他功能，例如缓存同步、并发更新、序列化、事件、性能计数器……开发人员只有在需要时才可以选择加入这些功能。

## 功能列表

- 一种处理不同缓存技术的通用接口：`ICache`
- 可配置
- 支持不同的缓存提供程序
- 现在可以配置序列化。仅在分布式缓存中才需要序列化。如果没有安装和配置额外的序列化包，将使用二进制序列化
- 使用分布式缓存的锁或事务更新值。
- 记录`CacheManager` 带有一个可扩展的记录 `API`
- 类型缓存接口。
- 多层 通过 `CacheManager` 管理多个缓存句柄，您可以轻松实现分层缓存
- 缓存区域：即使某些缓存系统不支持或不实现缓存区域，`CacheManager` 也会实现该机制。例如，这可用于对元素进行分组并一次删除所有元素。
- 统计信息：各种缓存操作的计数器。
- 性能计数器：为了能够检查某些数字` perfmon``，CacheManager ` 支持每个管理器实例和每个缓存句柄的性能计数器。
- 事件系统：`CacheManager` 触发常见缓存操作的事件：`OnGet`、`OnAdd`、`OnPut`、`OnRemove`、`OnClear`、`OnClearRegion`
- `System.Web.OutputCache`实现使用 `CacheManager` 作为 `OutputCache` 提供者，这使得 `OutputCache` 非常灵活，例如通过在许多 Web 服务器上使用像 Redis 这样的分布式缓存。
- 缓存客户端同步 使用 Redis 发布/订阅功能实现

## 例子

```csharp
private static void MostSimpleCacheManager()
{
    var config = new ConfigurationBuilder()
        .WithSystemRuntimeCacheHandle()
        .Build();

    var cache = new BaseCacheManager<string>(config);
    // or
    var cache2 = CacheFactory.FromConfiguration<string>(config);
}

private static void EventsExample()
{
    var cache = CacheFactory.Build<string>(s => s.WithDictionaryHandle());
    cache.OnAdd += (sender, args) => Console.WriteLine("Added " + args.Key);
    cache.OnGet += (sender, args) => Console.WriteLine("Got " + args.Key);
    cache.OnRemove += (sender, args) => Console.WriteLine("Removed " + args.Key);

    cache.Add("key", "value");
    var val = cache.Get("key");
    cache.Remove("key");
}
```

- Github 地址：[https://github.com/MichaCo/CacheManager](https://github.com/MichaCo/CacheManager)

最后大家如果喜欢我的文章，还麻烦给个关注并点个赞, 希望 net 生态圈越来越好！

![](https://img1.dotnet9.com/2022/07/0901.png)
