---
title: C# 人脸比对
slug: Csharp-face-comparison
description: 前面已经写过文字识别和人脸检测。都是在使用现成的轮子（直接调用的百度SDK），其实仔细看看文档，也就都知道怎么写了，而且百度还提供了多种语言的代码示例
date: 2022-01-20 14:12:04
copyright: Reprinted
author: Csharp小记
originalTitle: C# 人脸比对
originalLink: https://mp.weixin.qq.com/s/VM5L4cTnSljU-Z7rL5EgDA
draft: False
cover: https://img1.dotnet9.com/2022/01/cover_03.jpeg
categories: .NET
tags: 
    - C#
    - 人脸比对
---

## 前言

前面已经写过文字识别和人脸检测。都是在使用现成的轮子（直接调用的百度 SDK），其实仔细看看文档，也就都知道怎么写了，而且百度还提供了多种语言的代码示例；

所以如果后面没什么特殊需求的话，关于调用百度 API 来实现 AI 部分功能的代码就到此为止了；之所以写这三个，是因为这三种场景在日常生活、工作中使用的比较频繁。

人脸比对功能，一般会用在人脸与本人身份证做比对验证的情况下。

![](https://img1.dotnet9.com/2022/01/0301.jpg)

## 实现功能

- 验证两张人脸是否是同一个人

## 开发环境

- 开发工具：Visual Studio 2013

- .NET Framework 版本：4.5

## 实现代码

```C#
//从官网下载AipSdk.dll引用到自己项目
//API文档地址：https://cloud.baidu.com/doc/FACE/s/Lk37c1tpf

//填写自己账号的api_key和secret_key
string api_key = "", secret_key = "";

 private void btnCompare_Click(object sender, EventArgs e)
{
    if (pictureBox1.Image == null || pictureBox2.Image == null)
    {
        MessageBox.Show("请先复制图片到图片框");
        return;
    }

    Baidu.Aip.Face.Face client = new Baidu.Aip.Face.Face(api_key, secret_key);
    List<byte[]> list = new List<byte[]>();
    list.Add(ImageToByte((Bitmap)pictureBox1.Image));
    list.Add(ImageToByte((Bitmap)pictureBox2.Image));
    JObject result = client.Match(list);
    if ((int)result["result_num"]==0)
    {
        textBox1.Text = "匹配失败";
    }
    else
    {
        JArray jarr = (JArray)result["result"];
        string score = jarr[0]["score"].ToString();
        textBox1.Text = "匹配度：" + score;
    }
}

//复制图片方法
private Image CopyImage()
{
    try
    {
        Image image = null;
        IDataObject iData = Clipboard.GetDataObject();
        if (iData.GetDataPresent(DataFormats.FileDrop))
        {
            object obj = iData.GetData(DataFormats.FileDrop);
            image = Image.FromFile((obj as string[])[0].ToString());
        }
        else if (iData.GetDataPresent(DataFormats.Bitmap))
        {
            object obj = iData.GetData(DataFormats.Bitmap);
            image = obj as Image;
        }
        return image;
    }
    catch { return null; }

}

//图片转byte[]
public byte[] ImageToByte(Bitmap inImg)
{
    MemoryStream mstream = new MemoryStream();
    inImg.Save(mstream, ImageFormat.Bmp);
    byte[] bytes = new Byte[mstream.Length];
    mstream.Position = 0;
    mstream.Read(bytes, 0, bytes.Length);
    mstream.Close();
    return bytes;
}

private void pictureBox1_Click(object sender, EventArgs e)
{
    //pictureBox1获得焦点
    pictureBox1.Focus();
}
private void pictureBox2_Click(object sender, EventArgs e)
{
     //pictureBox2获得焦点
    pictureBox2.Focus();
}
private void pictureBox1_PreviewKeyDown(object sender, PreviewKeyDownEventArgs e)
{
    //粘贴图片到pictureBox1
    if (e.Control && e.KeyCode == Keys.V)
    {
        pictureBox1.Image = CopyImage();
    }
}
private void pictureBox2_PreviewKeyDown(object sender, PreviewKeyDownEventArgs e)
{
    //粘贴图片到pictureBox2
    if (e.Control && e.KeyCode == Keys.V)
    {
        pictureBox2.Image = CopyImage();
    }
}
```

## 实现效果

![](https://img1.dotnet9.com/2022/01/0302.gif)

根据百度的说法就是：相似度大于 80 一般会认为是同一个人。

大家在看文档的时候可能会发现官网提供的参数与我写的不一致，是因为官方提供了 V2 和 V3 两种 API。根据需要选择就行。

**由简入繁，拿来即用**

**后续精彩，持续关注**
