---
title: 开源C# WPF控件库《Panuon.UI.Silver》
slug: Panuon-UI-Silver
description: 国内优秀的WPF开源控件库，Panuon.UI的优化版本。一个漂亮的、使用样式与附加属性的WPF UI控件库，值得向大家推荐使用与学习。
date: 2019-12-04 13:45:56
author: 沙漠尽头的狼
banner: false
draft: false
cover: https://img1.dotnet9.com/2019/12/0402.gif
albums: 开源WPF
categories: .NET
copyright: Original
---

## 前言

国内优秀的WPF开源控件库，Panuon.UI的优化版本。一个漂亮的、使用样式与附加属性的WPF UI控件库，值得向大家推荐使用与学习。

今天站长(Dotnet9，站长网址：https://dotnet9.com, 微信公众号：dotnet9_com)推荐另一款开源的WPF控件库(PanuonUI.Silver，github链接： https://github.com/Panuon/PanuonUI.Silver )，一个年轻有为的国内小伙做的，非常优秀哦，值得向大家推荐。

本文介绍分两部分：第一部分，先介绍此控件库Case案例 `Morin 魔音`(http://www.huanghunxiao.com/) ，通过此案例，大家对此控件应该有个更深刻的印象；第二部分，详细介绍此控件库的特色控件，方便大家学习使用。

文中使用录制的gif展示相关界面，更形象的将此控件库、案例展示给大家，希望大家能够接受。

## 一. Case案例： Morin 魔音

说一千道一万，先展示该控件库的Case案例：`Morin 魔音`。一款非常不错的音乐播放软件，C# WPF做的哦，网址： http://www.huanghunxiao.com/ 。

下面展示该软件几个特色页面，大家可以通过上面的网址下载使用哦，真的很不错，站长已使用该软件几天，该软件作者（又一个年轻有为的国内IT星星）也在不断的优化更新中，希望大家多多支持。

### 1.1 Morin 魔音 关于页面

魔音Morin v2.3.7.0版，听歌用一个软件就够了，是不是很霸气！

![](https://img1.dotnet9.com/2019/12/0401.png)

### 1.2 Morin 魔音 默认页面

默认页面展示热门歌曲，图片+文字介绍，方便用户选择、播放，目前歌曲库来自QQ音乐、网易云音乐、酷我音乐、酷狗音乐等。

![](https://img1.dotnet9.com/2019/12/0404.gif) 

### 1.3 Morin 魔音 歌曲搜索

Morin 魔音提供方便的歌曲搜索功能，站长尝试搜索最近比较火的，陈伟霆的《野狼disco》，下面列出了相关的搜索结果，有无损mp3、mp4 MV供播放、下载选择。

![](https://img1.dotnet9.com/2019/12/0403.png)

## 1.4 Morin 魔音 MV播放页面

Morin 魔音 MV播放页面，不错吧。

![](https://img1.dotnet9.com/2019/12/0404.gif)

## 二. Panuon.UI.Silver特色控件介绍

Panuon.UI.Silver控件库的前身是Panuon.UI（ 一个好看精致、开源无限制使用的WPF控件库，github链接： https://github.com/Panuon/PanuonUI ），作者已停止维护，下图是作者声明：

![](https://img1.dotnet9.com/2019/12/0405.png)

所以本文主要介绍PanuonUI.Silver控件库，下面截图将近20张，用手机的朋友注意流量哦，希望不会浪费大家的流量，谢谢大家支持开源作者：PanuonUI.Silver。

对了，项目地址是：https://github.com/Mochengvia/PanuonUI.Silver，记住是这个地址哦。

### 2.1 控件库概览

控件库概览，只展示部分控件，后面将对部分特色控件再详细说明，更多控件还请大家访问 [PanuonUI.Silver](https://github.com/Mochengvia/PanuonUI.Silver)。

![](https://img1.dotnet9.com/2019/12/0406.gif)

### 2.2 按钮(Button)

老生常谈的，首先还是按钮，以动画展示。

![](https://img1.dotnet9.com/2019/12/0407.gif)

### 2.3 密码框（PasswordBox)

TextBox与PasswordBox类似，站长就只录制了密码框PasswordBox，支持输入时查看，有常用的图标、水印支持。

![](https://img1.dotnet9.com/2019/12/0408.gif)

### 2.4 复选框（CheckBox)

复选框CheckBox与RadioBox类似，动图展示比较形象，基础样式中的Switch，应该是大家比较喜欢的样式了，站长项目中也经常用。

![](https://img1.dotnet9.com/2019/12/0409.gif)

### 2.5 下拉框（ComboBox）及多选下拉框（MultiComboBox)

下拉框ComboBox，支持图标及文本搜索，很是方便。

![](https://img1.dotnet9.com/2019/12/0410.gif)

多选下拉框（MultiComboBox），这个在特殊业务场景下是很有用的，值得借鉴使用。

![](https://img1.dotnet9.com/2019/12/0411.gif)

### 2.6 进度条(ProgressBar)

进度条ProgressBar，提供两种样式，其中Ring样式非常实用，用户体验提升一个档次有木有？

![](https://img1.dotnet9.com/2019/12/0412.gif)

### 2.7 滑块(Slider)

滑块Slider，看着很爽吧，加上标尺很形象哦。

![](https://img1.dotnet9.com/2019/12/0413.gif)

### 2.8 分组框(GroupBox)

分组框GroupBox，附加控件常见吧，极大的减少个人封装了。

![](https://img1.dotnet9.com/2019/12/0414.gif)

### 2.9 TabControl
TabControl，提供三种基础样式选择，设置很灵活。

![](https://img1.dotnet9.com/2019/12/0415.gif)

### 2.10 Expander

Expander，增加了图标扩展。

![](https://img1.dotnet9.com/2019/12/0416.gif)

### 2.11 上下文菜单(ContextMenu)

上下文菜单ContextMenu，强大不？原生实现其实也不难，但使用此控件库可以大量缩短开发时间，再费点功夫可以实现360安全卫士的托盘右键菜单样式哦，强大吧？

![](https://img1.dotnet9.com/2019/12/0417.gif)

### 2.12 树状视图(TreeView)

树形视图TreeView，开发中也是经常用的一种控件 ，提供4种基础样式，也是非常不错的。

![](https://img1.dotnet9.com/2019/12/0418.gif)

### 2.13 表格控件(DataGrid)

表格控件DataGrid，提供了简单的调整功能，一般数据展示也够用了。

![](https://img1.dotnet9.com/2019/12/0419.gif)

### 2.14 分页控件（Pagination)

分页控件Pagination，使用表格时常用分页控件，但一般都是自己封装的，此控件封装的比较好，也提供了3种基础样式选择，也是十分够用了。

![](https://img1.dotnet9.com/2019/12/0420.gif)

### 2.15 等待提示框（PendingBox)

等待提示框PendingBox，费时操作比较实用，可提供取消操作，使用了遮罩，很酷吧，网页常用这种效果，桌面不常见，站长原来的项目也使用过类似的效果，但必须承认，该控件作者做的更顺畅。

![](https://img1.dotnet9.com/2019/12/0421.gif)

### 2.16 提示框（MessageBox)

提示框（MessageBox)，作者提供的选择比较多，有10多种，够用了，常见的Info、Error、Warning等提示都有。

![](https://img1.dotnet9.com/2019/12/0422.gif)

### 2.17 升级版气泡提示框(Notice)

升级版气泡提示框Notice，放弃使用Windows自带的气泡提示吧，即使是Windows 10自带的右下脚提示框，推荐使用这种方式。该效果在大多数软件中也常见，很流行呀。

![](https://img1.dotnet9.com/2019/12/0424.gif)

### 2.18 动画帮助类（AnimationHelper）

动画帮助类AnimationHelper，这不应该算是控件，算是扩展控件的帮助类，实用的很哟。

![](https://img1.dotnet9.com/2019/12/0424.gif)

### 2.19 轮播(Carousel)

轮播Carousel，常见网站首页Header主题介绍这样使用，站长首页也是类似的效果，朋友们可点击查看（网站首页），可用于广告展示，或者数据页面切换，其中的自动播放很有用。

![](https://img1.dotnet9.com/2019/12/0425.gif)

### 2.20 自定义窗体(WindowsX)

自定义窗体WindowsX，重点的都放在最后啦，哈哈，这个直接把整个软件提升一档有木有？

 

作者也是十分用心了，提供的样式都很酷，特别是最后一个播放器的界面，是不是和Case案例魔音MORIN很像？

![](https://img1.dotnet9.com/2019/12/0426.gif)

## 三、写在最后的话

感谢作者提供这么优秀的控件库。

再次给大家推荐两位作者的项目：

- 国内开源作者的控件库PanuonUI.Silver，采用C# WPF开发，github地址： https://github.com/Panuon/PanuonUI.Silver ，Panuon UI交流群QQ交流群：718778191。
- PanuonUI.Silver控件库的优秀案例魔音MORIN官网地址： http://www.huanghunxiao.com/ ，魔音Morin交流群：303537207。

再次谢谢两位作者（ PanuonUI.Silver ， 魔音MORIN ），希望大家多多支持，前往github、官网下载、加星、使用，多多推广两位年轻有为的IT星星。

![](https://img1.dotnet9.com/2019/12/0427.png)