---
title: 一个值得学习的WPF开源项目：Accelerider.Windows
slug: Accelerider-Windows
description: 此项目应用了Prism MVVM框架，项目展示数据来源于其他服务程序，使用的WebAPI通信，如果要正常运行此程序，需要您自己做一个WebAPI程序，由API接口提供数据驱动，其实直接查看代码最直接，有需要的自己是很方便提取的，即使项目无法运行。
date: 2019-12-05 13:45:56
author: 沙漠尽头的狼
banner: false
draft: false
cover: https://img1.dotnet9.com/2019/12/0304.png
tags: 
    - 开源WPF
albums:
    - WPF开源项目
categories: 
    - WPF
copyright: Original
---

## 项目介绍

此项目应用了Prism MVVM框架，项目展示数据来源于其他服务程序，使用的WebAPI通信，如果要正常运行此程序，需要您自己做一个WebAPI程序，由API接口提供数据驱动，其实直接查看代码最直接，有需要的自己是很方便提取的，即使项目无法运行。

## 项目特点

1. 此项目支持国际化，通过资源文件实现中、英、日三国语言切换，T4模板支持，但支持的不是很好，模块动态加载时（即不使用添加引用的方式），模块国际化不生效。

2. 控件使用的 MaterialDesignInXamlToolkit，一款非常优秀的开源WPF控件库，本人原来项目使用的原生+自定义模块、自定义控制、第三方控件（Dev Express\Telerik)等，自此又多了一个选择。

3. 此项目是采用了模块化的方式，方便动态扩展及删除功能，每个模块以一个dll的形式加载，具体界面效果请看下图Screenshots。

4. 此项目不是一个完整项目，作者只开源了部分功能，个人感觉作者有所保留，有比较多的缺憾，看下面的Screenshots看出端倪，Github上有部分功能模块未上传，已上传的功能也作了多多少少的删减，但也不失为一个优秀的开源项目，在此非常感谢作者，本人已使用.Netcore 3.0，以此项目为借鉴开发了一个项目。如何发现此优秀项目。

5. 其中一个库refit使用的非常好，对调用WebAPI很实用。

悔恨最近才看到这么优秀的项目，我自己是在2019年后才在github上搜索到该项目的，以前也不怎么浏览github，自己的WPF项目是自己“胡乱”搭的框架，虽然从2015年开始应用了Prism，MVVM也使用了，服务依赖注入也加上了，模块化也使用上了，但总是感觉不完美，自从关注该项目后，才明白了自己框架的不足，推荐朋友们研究、参考。

## 效果展示

本人偷懒，下面的截图直接从该项目的readme.md文件中复制的，需要研究该项目，请直接跳过去吧：[Accelerider.Windows](https://github.com/Accelerider/Accelerider.Windows)

### 操作动画

![](https://img1.dotnet9.com/2019/12/0301.png)

### 文件浏览

![](https://img1.dotnet9.com/2019/12/0302.png)

### 文件传输

![](https://img1.dotnet9.com/2019/12/0303.png)

### 左侧分组

![](https://img1.dotnet9.com/2019/12/0305.png)

### 应用商店

![](https://img1.dotnet9.com/2019/12/0304.png)

## 依赖

- MaterialDesignInXamlToolkit
- Unity Container
- Prism
- refit
- Newtonsoft.Json