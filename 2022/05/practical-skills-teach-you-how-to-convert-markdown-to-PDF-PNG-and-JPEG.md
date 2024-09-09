---
title: 【实用技巧】教你Markdown 转 PDF、PNG、JPEG
slug: practical-skills-teach-you-how-to-convert-markdown-to-PDF-PNG-and-JPEG
description: Markdown作为一种非常容易掌握的文档格式，正在被越来越多的人用来写文档，简历，博客等。
date: 2022-05-08 19:00:00
copyright: Reprinted
author: 青城 网上冲浪程序员
originalTitle: 【实用技巧】教你Markdown 转 PDF、PNG、JPEG
originalLink: https://mp.weixin.qq.com/s/keaj9WXM280F74lfQtPEQw
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_24.jpg
categories: 
    - 分享
tags: 
    - Markdown
---

**目录**

1. 前言
2. 今天主角
3. 总结
4. 站长补录

## 1. 前言

`Markdown`作为一种非常容易掌握的文档格式，正在被越来越多的人用来写文档，简历，博客等。

> 但是如果你要发文档的目标用户电脑上没有`Markdown`编辑器，那就无法看到预览效果。所以一般发给客户或者其他人，基本都是选择把`Markdown`文件转换成`PDF`等格式发送。

之前用`Typora`这个编辑器，自带了很好用的导出功能，但是现在这个编辑器已经是收费软件了。

## 2. 今天主角

现在`VS Code`已经牛逼的不行了，我们使用 `VS Code` + `Markdown Preview Enhanced` 插件就可以很方便的将`Markdown`文件导出为`PDF`等文件。

使用`VS Code`的这款插件甚至还能选择主题哦，使用方式也非常简单，下面介绍使用步骤，带您将一个`Markdown文件`导出为`PDF文件`：

1. 在`VS Code`里面装好了`Markdown Preview Enhanced`这个插件。

![](https://img1.dotnet9.com/2022/05/2401.png)

2. 重启`VS Code`

3. 新建一个`Markdown`文件，然后写下一段`Markdown`的文档，比如：

![](https://img1.dotnet9.com/2022/05/2402.png)

4. 在文档里点击鼠标右键：

![](https://img1.dotnet9.com/2022/05/2403.png)

5. 选择`MPE:打开侧边预览`，或者使用快捷键：Ctrl + K V（上面的是 Mac 快捷键），即可预览`Markdown`渲染效果：

![](https://img1.dotnet9.com/2022/05/2404.png)

6. 在预览窗口右键弹出菜单：

![](https://img1.dotnet9.com/2022/05/2405.png)

7. 点击菜单`Chrome(Puppeteer)->PDF`即可完成将`Markdown`文件导出为`PDF`文件，如图自动弹出了本机电脑默认的`PDF`预览工具：

![](https://img1.dotnet9.com/2022/05/2406.png)

## 3. 总结

目前`Markdown Preview Enhanced` 支持导出`PDF`、`PNG`、`JPEG`等格式。

给个小惊喜：可以使用最下边的 `Preview Theme`菜单选项，选择一个你喜欢的主题，再导出哦。

![](https://img1.dotnet9.com/2022/05/2413.gif)

当然，本文只介绍了这款插件的一小部分功能，它很强大的哦，期待您的尝试。

## 4. 站长补录

站长录制一张`Markdown Preview Enhanced`插件将`Markdown`文件导出为`PDF`文件的 Gif 动图，看看效果是不是很棒，默认的导出文件与`Markdown`原文件在同一目录下：

![](https://img1.dotnet9.com/2022/05/2408.gif)

站长平时使用[mdnice](https://mdnice.com/)编辑`Markdown`文件，它也有`PDF`文件导出功能：

![](https://img1.dotnet9.com/2022/05/2409.gif)

点开上图中的更多设置，可选择是否在页眉和页脚显示文件信息（文件导出时间、文件 Url 地址），一般是不勾选：

![](https://img1.dotnet9.com/2022/05/2411.png)

下面是`勾选`页眉和页脚选项导出的本文`PDF`文件：

![](https://img1.dotnet9.com/2022/05/2410.png)

`未勾选`页眉和页脚时导出的本文`PDF`文件：

![](https://img1.dotnet9.com/2022/05/2412.png)

- `VS Code`的这款`Markdown Preview Enhanced`插件优点在于免费，无水印，无需网络，安装好插件就可以使用，导出文件不只`PDF`，还能导出`PNG`、`JPEG`等。
- [mdnice](https://mdnice.com/)优势在于登录后可保存历史编辑文件，目前只能导出`PDF`文件，且只能在线使用，当然也是免费的。

至于使用哪个，看你喽（个人推荐`VS Code`这款插件，哈哈）。

欢迎关注我，不定时分享程序员实用小技巧

![](https://img1.dotnet9.com/2022/05/2407.jpg)
