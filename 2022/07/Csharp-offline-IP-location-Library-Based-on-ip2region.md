---
title: C#-基于ip2region的离线IP定位库
slug: Csharp-offline-IP-location-Library-Based-on-ip2region
description: 在开发中，我们需要记录关于登录者的ip和位置信息，可以通过ip2region来实现！
date: 2022-07-02 10:01:17
copyright: Reprinted
author: 黑哥聊dotNet
originaltitle: C#-基于ip2region的离线IP定位库
originallink: https://mp.weixin.qq.com/s/7eW4fQ-A_ZUsUlQD1vv8VQ
draft: False
cover: https://img1.dotnet9.com/2022/07/cover_01.jpeg
categories: .NET
tags: .NET
---

## 前言

在开发中，我们需要记录关于登录者的ip和位置信息，可以通过ip2region来实现！

比如说:

- qq，gitee等 我们在异地登录时，会提醒我们账号异地登录！
- 外卖等软件，需要地理位置，如果我们的gps关闭，也可以根据IP分析地理位置等

## 简介

ip2region - 准确率99.9%的离线IP地址定位库，0.0x毫秒级查询，ip2region.db数据库只有数MB，提供了java,php,c,python,nodejs,golang,c#等查询绑定和Binary,B树,内存三种查询算法。每条ip数据段都固定了格式：

- 城市Id|国家|区域|省份|城市|ISP_
- github地址:[https://github.com/lionsoul2014/ip2region](https://github.com/lionsoul2014/ip2region)

## 使用

- nuget安装

```shell
Install-Package IP2Region
```

- 下载ip2region.db

```shell
git clone https://github.com/lionsoul2014/ip2region.git
```

然后到data/文件夹下面找到`ip2region.db`复制到项目`resources`下

使用就很简单了，代码就一行

```csharp
static void Main(string[] args)
{
  using (var _search = new DbSearcher(Environment.CurrentDirectory + @"\DB\ip2region.db"))
  {
   Console.WriteLine(_search.MemorySearch("183.129.193.166").Region);
   Console.Read();
  }
}
```

## 总结

文章来源于即兴发挥，虽然我工作中现在还没遇到这种需求，但是了解一下总归是有好处的，欢迎大家留言讨论！