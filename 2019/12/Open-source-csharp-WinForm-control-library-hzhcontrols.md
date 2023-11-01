---
title: 开源C# Winform控件库《HZHControls》
slug: Open-source-csharp-WinForm-control-library-hzhcontrols
description: 一个基于.Net Framework 4.0，扁平化的、漂亮的、开源的C# Winform控件库。
date: 2019-12-15 13:45:56
author: 沙漠尽头的狼
banner: false
draft: false
cover: https://img1.dotnet9.com/2019/12/0105.gif
albums: 开源Winform
categories: Winform
copyright: Original
---

文章阅读导航 

- 一、写在前面的话
- 二、HZHControls介绍
  - 2.1 控件
    - 2.1.1 表单控件
    2.1.2 按钮
    2.1.3 选项卡
    2.1.4 树
    2.1.5 平铺列表
    2.1.6 导航
    2.1.7 横向列表
    2.1.8 自定义表格
    2.1.9 进度条
    2.1.10 步骤控件
    2.1.11 面包屑导航
    2.1.12 文字提示
    2.1.13 滚动文字
    2.1.14 滑块
    2.1.15 水波
    2.1.16 面板
    2.1.17 图标
    2.1.18 滚动条
    2.1.19 图片采样
    2.1.20 时间轴
    2.1.21 穿梭框
    2.1.22 引用区块
    2.1.23 表单验证
    2.1.24 分割线标签
  - 2.2 窗体
    2.2.1 提示窗体
    2.2.2 多输入窗体
    2.2.3 等待窗口
  - 2.3 图表
    2.3.1 组织结构图
    2.3.2 滚动图表
    2.3.3 雷达图
    2.3.4 金字塔图
    2.3.5 Live Charts
  - 2.4 工业控件
    2.4.1 LED文字
    2.4.2 仪表
    2.4.3 管道
    2.4.4 阀门
    2.4.5 鼓风机
    2.4.6 传送带
    2.4.7 警示灯
    2.4.8 箭头
    2.4.9 温度计
- 三、HZHControls获取
  - 3.1 官方帮助文档、博客
  - 3.2 技术交流群
  - 3.3 源码获取
- 四、写在后面的话

## 一、写在前面的话

小编写的前3篇介绍开源C# WPF控件库文章发布后，反响热烈，谢谢大家支持。

![](https://img1.dotnet9.com/2019/12/0101.png)

文章评论区有网友留言：“推荐一个传统的Winform的控件库吧”，小编我是左右为难（不是男），虽然小编使用Winform开发4年+，但常用的是收费控件库[DevExpress](https://www.devexpress.com/)、[Telerik](https://www.telerik.com/)等，自定义的控件库使用不多，原因有两点：

1. 优秀的开源控件库实在太少。
2. Winform自定义控件相较WPF自定义控件要麻烦许多。
3. 小编客户需求不强烈。

![](https://img1.dotnet9.com/2019/12/0102.png)

在机缘巧合的情况下，某个WPF群群友发了Winform控件库《HZHControls》的全球同性交友网站Gayhub地址（下载该控件库请点击[Github地址](https://github.com/kwwwvagaa/NetWinformControl)），小编当然毫不犹豫的点开欣赏啦，所以本篇文章由此而来。

**《HZHControls》Github仓库**

![](https://img1.dotnet9.com/2019/12/0103.png)

**《HZHControls》Gitee仓库**

![](https://img1.dotnet9.com/2019/12/0104.png)

小编推荐《HZHControls》控件库理由如下：

1. 一个基于.Net Framework 4.0，扁平化的、漂亮的、开源的C# Winform控件库。
2. 纯原生开发，不包含第三方插件和类库。包含了常用窗体和常用控件，以及工业工具，类Web控件，使用该控件库可以快速的搭建一个漂亮的应用程序。
3. 漂亮：控件UI以漂亮的Web控件为范本，让你做出来的应用更有WEB感觉，更加扁平化，更加漂亮。
4. 开源： 控件完全开源，你可以高度自由的根据自己的需求来扩展或修改控件。
5. 控件收费吗？ 基于开源协议GPL-3.0协议，对非商业用途是完全免费的，同样也具有更低的商业授权费用。

## 二、HZHControls介绍

### 2.1 控件

包含常规的文本框、水印文字、数字框、输入验证、表单验证、下拉框、时间选择框、滑块、文字提示、面板等。

### 2.1.1 表单控件

![](https://img1.dotnet9.com/2019/12/0105.gif)

### 2.1.2 按钮

![](https://img1.dotnet9.com/2019/12/0106.png)

![](https://img1.dotnet9.com/2019/12/0107.png)

### 2.1.3 选项卡

![](https://img1.dotnet9.com/2019/12/0108.png)

### 2.1.4 树

树和列表控件类似，下面是HZC控件库中的树形控件：

![](https://img1.dotnet9.com/2019/12/0109.png)

### 2.1.5 平铺列表

![](https://img1.dotnet9.com/2019/12/0110.png)

### 2.1.6 导航

包含水平和垂直导航菜单：

![](https://img1.dotnet9.com/2019/12/0111.png)

HZH控件库导航菜单

![](https://img1.dotnet9.com/2019/12/0112.gif)

HZH控件库 垂直导航

![](https://img1.dotnet9.com/2019/12/0113.png)

### 2.1.7 横向列表

![](https://img1.dotnet9.com/2019/12/0114.png)

### 2.1.8 自定义表格

普通表格及树表格：

![](https://img1.dotnet9.com/2019/12/0115.png)

HZH控件库 自定义表格

![](https://img1.dotnet9.com/2019/12/0116.png)

HZH控件库 树表格

### 2.1.9 进度条

各式各样的酷炫进度条：

![](https://img1.dotnet9.com/2019/12/0117.gif)

### 2.1.10 步骤控件

![](https://img1.dotnet9.com/2019/12/0118.png)

### 2.1.11 面包屑导航

![](https://img1.dotnet9.com/2019/12/0119.png)

### 2.1.12 文字提示

![](https://img1.dotnet9.com/2019/12/0120.gif)

### 2.1.13 滚动文字

![](https://img1.dotnet9.com/2019/12/0121.gif)

### 2.1.14 滑块

![](https://img1.dotnet9.com/2019/12/0122.gif)

### 2.1.15 水波

![](https://img1.dotnet9.com/2019/12/0123.gif)

### 2.1.16 面板

![](https://img1.dotnet9.com/2019/12/0124.png)

### 2.1.17 图标

HZHControl包含两种图标：Awesome和Elegant。

HZH控件库 Awesome图标

![](https://img1.dotnet9.com/2019/12/0125.png)

HZH控件库 Elegant图标

![](https://img1.dotnet9.com/2019/12/0126.png)

### 2.1.18 滚动条

包含列表、文本框、水平|竖直滚动条：

![](https://img1.dotnet9.com/2019/12/0127.png)

### 2.1.19 图片采样

![](https://img1.dotnet9.com/2019/12/0129.gif)

### 2.1.20 时间轴

![](https://img1.dotnet9.com/2019/12/0129.png)

### 2.1.21 穿梭框

![](https://img1.dotnet9.com/2019/12/0130.gif)

### 2.1.22 引用区块

![](https://img1.dotnet9.com/2019/12/0131.png)

### 2.1.23 表单验证

![](https://img1.dotnet9.com/2019/12/0132.png)

### 2.1.24 分割线标签

![](https://img1.dotnet9.com/2019/12/0133.png)

## 2.2 窗体

窗体包含常用的提示框、具有后退的窗体，具有确定取消按钮的窗体、多线程等待窗体、资源加载窗体等常用窗体，以下只展示部分效果图。

### 2.2.1 提示窗体

![](https://img1.dotnet9.com/2019/12/0134.png)

### 2.2.2 多输入窗体

![](https://img1.dotnet9.com/2019/12/0135.png)

### 2.2.3 等待窗口

![](https://img1.dotnet9.com/2019/12/0136.gif)

## 2.3 图表

包含动态图标，多坐标系图标，柱状图，饼状图，折线图，组织结构图等应有尽有，满足你的数据展示。

### 2.3.1 组织结构图

![](https://img1.dotnet9.com/2019/12/0137.gif)

### 2.3.2 滚动图表

![](https://img1.dotnet9.com/2019/12/0138.gif)

### 2.3.3 雷达图

![](https://img1.dotnet9.com/2019/12/0139.png)

### 2.3.4 金字塔图

![](https://img1.dotnet9.com/2019/12/0140.png)

### 2.3.5 Live Charts

多达29种Live Charts：

![](https://img1.dotnet9.com/2019/12/0141.png)

现有Live Charts Demo

以下展示部分Live Charts，更多样例请稳步gitee或github下载：

Basic Stacked Bar

![](https://img1.dotnet9.com/2019/12/0142.png)

BasicBubbles

![](https://img1.dotnet9.com/2019/12/0143.png)

BasicLine

![](https://img1.dotnet9.com/2019/12/0149.png)

Financial

![](https://img1.dotnet9.com/2019/12/0144.png)

FlunnelChart

![](https://img1.dotnet9.com/2019/12/0145.png)

GanttChart

GanttChart即甘特图，功能虽没有Office Project那么强大，但能使用Winform自己实现确实很了不起了：

![](https://img1.dotnet9.com/2019/12/0146.png)

Gauge-360

![](https://img1.dotnet9.com/2019/12/0147.png)

AngularGauge

![](https://img1.dotnet9.com/2019/12/0148.png)

GeoMapExample

![](https://img1.dotnet9.com/2019/12/0150.gif)

DoughnutExample

![](https://img1.dotnet9.com/2019/12/0151.gif)

PieChartExample

![](https://img1.dotnet9.com/2019/12/0152.gif)

## 2.4 工业控件

包含 管道、风机、警灯、阀门、LED文章，仪表，传送带，温度计，还有取样控件等全覆盖。

### 2.4.1 LED文字

![](https://img1.dotnet9.com/2019/12/0153.gif)

### 2.4.2 仪表

![](https://img1.dotnet9.com/2019/12/0154.png)

### 2.4.3 管道

![](https://img1.dotnet9.com/2019/12/0155.gif)

### 2.4.4 阀门

![](https://img1.dotnet9.com/2019/12/0156.gif)

### 2.4.5 鼓风机

![](https://img1.dotnet9.com/2019/12/0157.png)

### 2.4.6 传送带

![](https://img1.dotnet9.com/2019/12/0158.gif)

### 2.4.7 警示灯

![](https://img1.dotnet9.com/2019/12/0159.gif)

### 2.4.8 箭头

![](https://img1.dotnet9.com/2019/12/0160.png)

### 2.4.9 温度计

![](https://img1.dotnet9.com/2019/12/0161.png)

## 三、HZHControls获取

### 3.1 官方帮助文档、博客

官方网站： http://www.hzhcontrols.com/ 。

![](https://img1.dotnet9.com/2019/12/0162.png)

控件帮助文档： http://www.hzhcontrols.com/doc.html 。

![](https://img1.dotnet9.com/2019/12/0163.png)

HZH控件库帮助文档

作者博客园： https://www.cnblogs.com/bfyx/ 。

![](https://img1.dotnet9.com/2019/12/0164.png) 


### 3.2 技术交流群

作者QQ： [623128629](https://wpa.qq.com/msgrd?v=3&uin=623128629&site=qq&menu=yes)。

QQ交流群： [568015492](https://wpa.qq.com/msgrd?v=3&uin=568015492&site=qq&menu=yes) 。

### 3.3 源码获取

github： https://github.com/kwwwvagaa/NetWinformControl 。

![HZH控件库github仓库](https://img1.dotnet9.com/2019/12/0165.png) 

gitee： https://gitee.com/kwwwvagaa/net_winform_custom_control 。

![HZH控件库gitee仓库](https://img1.dotnet9.com/2019/12/0166.png) 

## 四、写在后面的话

开源C# Winform控件库实在不多，本库也确实优秀，做Winform的朋友可以参考学习、借鉴，特别是控件库作者的博客，是学习Winform自定义控件的好地方。

再优秀的控件库也会存在Bug，作者开源控件库不易，毕竟个人能力有限，朋友们如遇到使用问题或者发现控件Bug，欢迎联系作者或者在QQ技术交流群中提问，控件库作者及小编非常欢迎大家相互学习讨论。