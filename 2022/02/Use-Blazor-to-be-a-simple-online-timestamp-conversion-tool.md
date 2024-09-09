---
title: 使用Blazor做个简单的时间戳在线转换工具
slug: Use-Blazor-to-be-a-simple-online-timestamp-conversion-tool
description: 时间戳转换，关键点在于双向绑定`@bind-Value`，就简单贴源码吧
date: 2022-02-27 23:20:12
copyright: Original
originalTitle: 使用Blazor做个简单的时间戳在线转换工具
draft: False
cover: https://img1.dotnet9.com/2022/02/cover_17.jpeg
categories: 
    - .NET
tags: 
    - .NET 7
    - Blazor
    - 时间戳转换
    - 双向绑定
---

# 时间戳转换

沙漠尽头的狼 - 2022-02-27 23:20:12 - ![dotnet-version](https://img.shields.io/badge/.NET%207.0-blue) ![Visual Studio 2022](https://img.shields.io/badge/Visual%20Studio%20-2022-blueviolet) <a target="_blank" href="https://qm.qq.com/cgi-bin/qm/qr?k=iL6egdGSGCMPezcUyzMPEcs9qsllgwr-&jump_from=webapi"><img border="0" src="//pub.idqqimg.com/wpa/images/group.png" alt="Dotnet9软件技术交流" title="Dotnet9软件技术交流"></a> [![码云](https://img.shields.io/badge/Gitee-%E7%A0%81%E4%BA%91-orange)](https://gitee.com/dotnet9/dotnet9.com.git) [![Github](https://img.shields.io/badge/%20-github-%2324292e)](https://github.com/dotnet9/dotnet9.com) [![Github stars](https://img.shields.io/github/stars/dotnet9/dotnet9.com)](https://github.com/dotnet9/dotnet9.com)

![](https://img1.dotnet9.com/2022/02/1701.jpg)

> 时间戳转换，关键点在于双向绑定`@bind-Value`，就简单贴源码吧

`TimestampTool.razor`

```html
@page "/timestamp"
@using BlazorComponent.I18n
@layout PublicLayout

<PageTitle>@T("TimestampToolTitle")</PageTitle>

<h2 style="margin-bottom: 10px; margin-top: 10px; text-align: center;">@T("TimestampToolDesc")</h2>

<MRow>
    @T("TimestampToolDateNow") @DateToTimestamp(DateTime.Now, TimestampKind.Seconds)
</MRow>
<MRow>
    <MTextField Label="@T("TimestampToolTimestamp")" TValue="long" @bind-Value="@_timestamp1"/>
    <MSelect @bind-Value="@_kindValue1"
             Label="@T("TimestampToolTimestampKind")"
             Items="@_items"
             ItemText="u => u.Label"
             ItemValue="u => u.Value"
             Class="mx-3"
             MenuProps="props => props.OffsetY = true">
    </MSelect>
    <MButton OnClick="@Convert1">@T("TimestampToolConvert")</MButton>
    <MTextField Label="@T("TimestampToolBeijingTime")"
                TValue="string" @bind-Value="@_datetime1"
                Class="ml-3"/>
</MRow>
<MRow>
    <MTextField Label="@T("TimestampToolBeijingTime")" TValue="string" @bind-Value="@_datetime2"/>
    <MButton Class="mx-3" OnClick="@Convert2">@T("TimestampToolConvert")</MButton>
    <MTextField Label="@T("TimestampToolTimestamp")" TValue="long" @bind-Value="@_timestamp2"/>
    <MSelect @bind-Value="@_kindValue2"
             Label="@T("TimestampToolTimestampKind")"
             Items="@_items"
             ItemText="u => u.Label"
             ItemValue="u => u.Value"
             MenuProps="props => props.OffsetY = true"
             Class="ml-3">
    </MSelect>
</MRow>

<MarkdownComponent
    LocalPostFilePath="wwwroot/2022/02/2022-02-27_03.md"
    SourceCodeUrl="https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools.Web/Pages/Public/TimeTools/TimestampTool.razor"/>

@code
{
    [Inject]
    private I18n I18N { get; set; } = default!;

    private DateTime _currentDatetime;
    private long _timestamp1;
    private long _timestamp2;
    private string? _datetime1;
    private string? _datetime2;
    private TimestampKind _kindValue1;
    private TimestampKind _kindValue2;

    private readonly List<TimestampItem> _items = new();

    protected override Task OnInitializedAsync()
    {
        _items.Add(new TimestampItem(T("TimestampToolKindSeconds")!, TimestampKind.Seconds));
        _items.Add(new TimestampItem(T("TimestampToolKindMilliseconds")!, TimestampKind.Milliseconds));
        _currentDatetime = DateTime.Now;
        _timestamp1 = _timestamp2 = DateToTimestamp(_currentDatetime, TimestampKind.Seconds);
        _datetime1 = _datetime2 = _currentDatetime.ToString("yyyy-MM-dd HH:mm:ss");
        return base.OnInitializedAsync();
    }

    private void Convert1()
    {
        _datetime1 = TimestampToDate(_timestamp1, _kindValue1).ToString(_kindValue1 == TimestampKind.Seconds ? "yyyy-MM-dd HH:mm:ss" : "yyyy-MM-dd HH:mm:ss.fff");
    }

    private void Convert2()
    {
        try
        {
            _timestamp2 = DateToTimestamp(DateTime.Parse(_datetime2), _kindValue2);
        }
        catch
        {
        }
    }

    private static long DateToTimestamp(DateTime date, TimestampKind kind)
    {
        try
        {
            var point = new DateTime(1970, 1, 1);
            var time = date.Subtract(point);

            return (long)(kind == TimestampKind.Seconds ? time.TotalSeconds : time.TotalMilliseconds);
        }
        catch
        {
            return default;
        }
    }


    private static DateTime TimestampToDate(long timestamp, TimestampKind kind)
    {
        try
        {
            var point = new DateTime(1970, 1, 1);
            var time = kind == TimestampKind.Seconds ? point.AddSeconds(timestamp) : point.AddMilliseconds(timestamp);

            return time;
        }
        catch
        {
            return default;
        }
    }

    public string? T(string key)
    {
        return I18N.LanguageMap.GetValueOrDefault(key);
    }

    enum TimestampKind
    {
        Seconds,
        Milliseconds
    }

    class TimestampItem
    {
        public string Label { get; }
        public TimestampKind Value { get; }

        public TimestampItem(string label, TimestampKind value)
        {
            Label = label;
            Value = value;
        }
    }
}
```

`Dotnet9工具箱`会不断添加新的免费、开源、在线工具，欢迎 star 支持，有什么需求我会考虑加上，仓库地址：[Dotnet9.Tools](https://github.com/dotnet9/dotnet9.com)，可[提交 issue](https://github.com/dotnet9/dotnet9.com/issues/new)、[网站留言](https://dotnet9.com)、微信公众号(dotnet9)联系等等。

> 本工具源码：[TimestampTool](https://github.com/dotnet9/dotnet9.com/blob/develop/src/Dotnet9.Tools.Web/Pages/Public/TimeTools/TimestampTool.razor)
>
> 介绍文章：[使用 Blazor 做个简单的时间戳在线转换工具](https://dotnet9.com/?p=1801)
>
> 在线演示地址：[https://tool.dotnet9.com/timestamp](https://tool.dotnet9.com/timestamp)
