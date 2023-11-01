---
title: .NET下如何拦截鼠标、键盘消息？Win32NET来帮你
slug: How-to-intercept-mouse-and-keyboard-messages-under-Net-Win32net-to-help-you
description: Win32NET是一个Win32API的.NET下封装的类库
date: 2022-01-20 14:12:04
copyright: Reprinted
author: 明月心技术学堂
originaltitle: .NET下如何拦截鼠标、键盘消息？Win32NET来帮你
originallink: https://mp.weixin.qq.com/s/_c4T7R_HeVLVg6sqvLoABw
draft: False
cover: https://img1.dotnet9.com/2022/01/cover_04.jpeg
categories: .NET相关
tags: 拦截鼠标,拦截键盘
---

Win32NET是一个Win32API的.NET下封装的类库，包含：

1. 常用win32的API的net封装
2. 鼠标、键盘、热键hook钩子模块，
3. 模拟键盘输入文字（支持各种字符文字、不同语言的文字）、模拟鼠标点击移动滚动等操作
4. 系统硬件信息查询

如何使用该Win32Net库呢？可以在nuget包管理搜索Win32Net，

```shell
Install-Package Win32Net -Version 1.2.0
```

或者直接添加引用，

```shell
<PackageReference Include="Win32Net" Version="1.2.0" /
```

**如何使用鼠标钩子：**

首先实例化一个鼠标钩子对象，然后定义鼠标事件回调方法，启动监听即可。当不需要继续监听鼠标信息，则可以取消监听。

```C#
win32.Hooks.MouseHook mouseHook = new Hooks.MouseHook();//实例化鼠标钩子对象
mouseHook.LeftDown += MouseHook_LeftDown;//鼠标左键按下的事件监听回调方法
mouseHook.Start();//开始监听
mouseHook.Stop();//停止监听
```

**如何使用键盘钩子：**

键盘钩子与鼠标钩子使用类似，首先实例化一个键盘钩子对象，然后定义键盘事件回调方法，启动监听即可。当不需要继续监听键盘信息，则可以取消监听。

```C#
 Win32.Hooks.KeyboardHook keyboardHook = new Win32.Hooks.KeyboardHook();
  keyboardHook.OnKeyUp += KeyboardHook_OnKeyUp;
  keyboardHook.Start();
  keyboardHook.Stop();
```

**如何注册全局快捷键**

```C#
//WPF 窗口句柄
//IntPtr hwnd =   new WindowInteropHelper(WPFWindow对象).Handle;
//winform 窗口句柄
 IntPtr hwnd = this.Handle;
  //实例化热键对象，需要一个句柄，用于接收消息
 Win32.Hooks.SystemHotKey systemHotKey = new Win32.Hooks.SystemHotKey(hwnd);
//热键id,要求唯一
 int hotKeyId = 5000;
//注册Alt+Q快捷键
systemHotKey.AddHotKey(hotKeyId, Win32.KeyModifiers.Alt, Keys.Q,
 () =>
 {
 MessageBox.Show("你按了Alt+Q快捷键");
 }
);
//注册ESC快捷键
 systemHotKey.AddHotKey(hotKeyId + 1, Win32.KeyModifiers.None, Keys.Escape,
 () =>
 {
 this.Close();
 }
 );
```

如何获取系统硬件信息

```C#
SystemInfo systemInfo = new SystemInfo();
richTextBox1.AppendText("操作系统：" + systemInfo.operatingSystem.Caption + "\n");
richTextBox1.AppendText("系统ID：" + systemInfo.operatingSystem.SerialNumber + "\n");
richTextBox1.AppendText("操作系统平台：" + systemInfo.operatingSystem.OSLevel + "\n");
richTextBox1.AppendText("系统安装时间：" + systemInfo.operatingSystem.InstallDate + "\n");
richTextBox1.AppendText("系统最近启动时间：" + systemInfo.operatingSystem.LastBootUpTime + "\n");
richTextBox1.AppendText("系统时间：" + systemInfo.operatingSystem.LocalDateTime + "\n");
richTextBox1.AppendText("CPU：" + systemInfo.processor.Name + "\n");
richTextBox1.AppendText("CPU厂商：" + systemInfo.processor.Manufacturer + "\n");
richTextBox1.AppendText("CPU序列号：" + systemInfo.processor.SerialNumber + "\n");
richTextBox1.AppendText("物理内存：" + systemInfo.memory.TotalPhysicalMemory + "\n");
```