---
title: (24/30)大家一起学Blazor：<AuthorizeRouteView>运用
slug: 24-of-30-let-us-learn-blazor-together-authorizetouteview
description: 前天有在`Blog.razor`加入通过验证及未通过的显示内容，但如果系统要呈现的未通过验证内容都一样，在每个Component 都这样写就太浪费时间了，可以利用`App.razor`帮我们做统一呈现的事。
date: 2021-12-23 22:51:24
copyright: Reprinted
author: StrayaWorker
originaltitle: (24/30)大家一起学Blazor：<AuthorizeRouteView>运用
originallink: https://ithelp.ithome.com.tw/articles/10271129
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
categories: .NET
tags: Blazor Server,学Blazor
---

前天有在`Blog.razor`加入通过验证及未通过的显示内容，但如果系统要呈现的未通过验证内容都一样，在每个Component 都这样写就太浪费时间了，可以利用`App.razor`帮我们做统一呈现的事。

先把`Blog.razor`原本包覆内容的`<AuthorizeView>`删除，剪切`<NotAuthorized>`的部分，再加上`@attribute [Authorize]`这行，告知`Blazor` 这个Component 需要验证。

![](https://img1.dotnet9.com/2021/12/3601.png)

再将App.razor原本的单标签(single tag)改成配对标签(paired tags)，贴上刚刚剪切的<NotAuthorized>，这时候再启动网站，未验证情况下看到的内容会一样。

![](https://img1.dotnet9.com/2021/12/3602.png)

目前只要通过验证的用户都能看到相同Component，不过大部分系统通常都会有角色或是权限划分，以区分不同用户，明天就来尝试用`Role` 区分授权。

**注：本文代码通过 .NET 6 + Visual Studio 2022重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**