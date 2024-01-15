---
title: Blazor开源组件库 - Ant Design Blazor
slug: Another-powerful-tool-for-client-side-web-development-with-dotNet-Ant-Design-Blazer
description: 继上篇介绍Bootstrap风格的BlazorUI组件库后，今天我来介绍另一款Blazor UI组件库：一套基于 Ant Design 和 Blazor 的企业级组件库。
date: 2020-06-21 13:45:56
copyright: Original
banner: true
originaltitle: .NET进行客户端Web开发又一利器 - Ant Design Blazor
draft: False
cover: https://img1.dotnet9.com/2020/06/0109.jpg
albums: 开源Blazor
categories: Blazor
tags: Blazor
---

你好，我是Dotnet9，继上篇介绍Bootstrap风格的BlazorUI组件库后，今天我来介绍另一款Blazor UI组件库：一套基于 Ant Design 和 Blazor 的企业级组件库。

本文导航：

- 一、关于Ant Design Blazor
- 二、Ant Design Blazor的社区贡献
  - 2.1 项目关注度
  - 2.2 Ant Design官方认可
  - 2.3 微软官方认可
- 三、Ant Design Blazor UI库介绍
- 四、Ant Design Blazor后续计划
- 五、Ant Design Blazor技术交流

## 一、关于Ant Design Blazor

![](https://img1.dotnet9.com/2020/06/0101.png)

项目名称：Ant Design Blazor

项目作者：James Yeung（社区发起者，目前项目参与度高，有较多贡献者）

开源许可协议：MIT

项目地址：https://github.com/ant-design-blazor/ant-design-blazor

✨ 特性

- 提炼自企业级中后台产品的交互语言和视觉风格。
- 开箱即用的高质量 Blazor 组件，可在多种托管方式共享。
- 支持基于 WebAssembly 的客户端和基于 SignalR 的服务端 UI 事件交互。
- 支持渐进式 Web 应用（PWA）
- 使用 C# 构建，多范式静态语言带来高效的开发体验。
- ⚙️ 基于 .NET Standard 2.1，可直接引用丰富的 .NET 类库。
- 可与已有的 http://ASP.NET Core MVC、Razor Pages 项目无缝集成。

**关于开源协议：MIT**

参考[百度百科](https://baike.baidu.com/item/MIT%25E8%25AE%25B8%25E5%258F%25AF%25E8%25AF%2581/6671281%3Ffr%3Daladdin)

>被授权人权利
>
>被授权人有权利使用、复制、修改、合并、出版发行、散布、再授权及贩售软件及软件的副本。
>
>被授权人可根据程序的需要修改授权条款为适当的内容。
>
>被授权人义务
>
>在软件和软件的所有副本中都必须包含版权声明和许可声明。
>
>其他重要特性
>
>此授权条款并非属copyleft的自由软件授权条款，允许在自由/开放源码软件或非自由软件（proprietary software）所使用。
>
>MIT的内容可依照程序著作权者的需求更改内容。此亦为MIT与BSD（The BSD license, 3-clause BSD license）本质上不同处。
>
>MIT条款可与其他授权条款并存。另外，MIT条款也是自由软件基金会（FSF）所认可的自由软件授权条款，与GPL兼容。

## 二、Ant Design Blazor的社区贡献

该库是国内目前社区宣传度做的最好的一款Blazor UI组件库，对于Blazor的社区推广起到很大的作用，Dotnet9是通过该库作者的一篇文章[《如何用 Blazor 实现 Ant Design 组件库？》](https://mp.weixin.qq.com/s/Bhvp4EKgKh-lMyh6v3_Ayg)开始关注Blazor的，关于该库作者的心路历程，大家可点击原文了解。

![](https://img1.dotnet9.com/2020/06/0102.jpg)

距离作者发文已有3月之久，文中作者的部分期望应该说是实现了一个个小目标了，也体现在了对社区的贡献上（对Blazor推广作用）：

### 2.1 项目关注度

作者将库发布在Github上，README支持中英文，日常代码提交使用英文，让全球的.Neter参与其中，使得更多的社区成员开始关注Ant Design Blazor，也使得更多的社区成员开始关注Blazor的发展了。

库作者发文时star统计（2020年03月21日）

![](https://img1.dotnet9.com/2020/06/0103.jpg)

3个月后的今天star统计（2020年06月20日）

![](https://img1.dotnet9.com/2020/06/0104.jpg)

【更新】站长整理文章时再统计，已经到了2022年2月20号晚23：49，隔了1年半之久，star从1.3K到了4k，牛逼哟，赶紧关注该库吧。

![](https://img1.dotnet9.com/2020/06/0105.png)


### 2.2 Ant Design官方认可

原文作者的小期望：

>在为了与官方高度一致上的努力，还会继续。希望有一天能在丰富 Blazor 生态的同时，还能成为被 Ant Design 生态认可的框架实现，能成为他们 Design 梦的一个延续。

Ant Design官方前端实现介绍[链接](https://ant.design/docs/spec/introduce-cn%23%25E5%2589%258D%25E7%25AB%25AF%25E5%25AE%259E%25E7%258E%25B0)

![](https://img1.dotnet9.com/2020/06/0106.jpg)

### 2.3 微软官方认可

微软Build2020开发者大会Blazor介绍中，提及Ant Design Pro。

一图胜千言，得到微软认可是对作者最大的奖励，也是对社区的最好宣传。

![](https://img1.dotnet9.com/2020/06/0107.jpg)

## 三、Ant Design Blazor UI库组件介绍

Ant Design Blazor UI组件浏览地址：https://ant-design-blazor.github.io/

Ant Design Blazor的开发初衷是尽量与Ant Design组件库一致，可对比查看：Ant Design

下面只对部分组件截图介绍，更多组件请戳上面链接查看：

### 3.1 首页介绍

网站风格和Ant Design官网高度一致，更方便熟悉Ant Design组件的朋友使用。

![](https://img1.dotnet9.com/2020/06/0108.jpg)

### 3.2 组件概览

组件整体印象，这只是其中一部分，丰富的组件需要点击[Ant Design Blazor](https://ant-design-blazor.github.io/)了解更多哟。

![](https://img1.dotnet9.com/2020/06/0109.jpg)

## 四、Ant Design Blazor后续计划

目前组件开发基本已经完成，可应用于常规项目开发，组件库后续计划：

- 6月底发布0.1版本；
- 添加测试、完善文档、企业级应用和反馈；
- 完成一个开箱即用的模板（伟大目标，像Ant Design Pro靠拢）；
- 添加页面生成工具，类似UMI添加block，查看Ant Design的区块介绍。

## 五、Ant Design Blazor技术交流

- 微信群 可添加作者微信号拉你入群：JamesYeungMVP
- 钉钉群

![](https://img1.dotnet9.com/2020/06/0110.png)

**站长更新（2022年02月20号 23：55）**

站长今天更新网站时，该项目已经大变样，具体的请看该项目仓库：https://github.com/ant-design-blazor/ant-design-blazor