---
title: 可用于智能客服的完全开源免费商用的知识库项目
slug: a-fully-open-source-free-commercial-knowledge-base-project-that-can-be-used-for-intelligent-customer-service
description: 我们的目标是提供一个能够理解和处理复杂查询的智能搜索解决方案，帮助用户快速准确地获取所需信息。
date: 2024-02-29 04:57:13
lastmod: 2024-02-29 05:18:43
copyright: Reprinted
banner: false
author: token的技术分享
originalTitle: 可用于智能客服的完全开源免费商用的知识库项目
originalLink: https://mp.weixin.qq.com/s/ANqHugTR1-rpQEaz2XF6qg
draft: false
cover: https://img1.dotnet9.com/2024/02/0311.png
categories: .NET
tags: .NET,Blazor,Web API,FastWiki,Semantic Kernel,MasaFramework,MasaBlazor,HttpClient,MiniApis,PostgreSQL
---

## **介绍**

`FastWiki`项目是一个高性能、基于最新技术栈的知识库系统，专为大规模信息检索和智能搜索设计。利用微软 Semantic Kernel 进行深度学习和自然语言处理，结合.NET 8 和`MasaBlazor`前端框架，后台采用`.NET 8`+`MasaFramework`+`SemanticKernel`，实现了一个高效、易用、可扩展的智能向量搜索平台。我们的目标是提供一个能够理解和处理复杂查询的智能搜索解决方案，帮助用户快速准确地获取所需信息。

## **技术栈**

- 前端框架：`MasaBlazor`通过`HttpClient`实现前后分离
- 后端框架：`MasaFramework `基于 .NET 8 使用`MiniApis`实现`webApi`功能，并且更高的性能
- 向量搜索引擎：使用 `PostgreSQL `的向量插件，优化搜索性能
- 深度学习与 NLP：微软 Semantic Kernel，提升搜索的语义理解能力
- 许可证：Apache-2.0，鼓励社区贡献和使用

## **特点**

- 智能搜索：借助 Semantic Kernel 的深度学习和自然语言处理技术，能够理解复杂查询，提供精准的搜索结果。
- 高性能：通过 pgsql 的向量插件优化向量搜索性能，确保即使在大数据量下也能快速响应。
- 现代化前端：使用 MasaBlazor 前端框架，提供响应式设计和用户友好的界面。
- 强大的后端：基于最新的.NET 8 和 MasaFramework，确保了代码的高效性和可维护性。
- 开源和社区驱动：采用 Apache-2.0 许可证，鼓励开发者和企业使用和贡献。

## **项目部分截图介绍**

添加知识库：

![图片](https://img1.dotnet9.com/2024/02/0301.png)

上传我们的文档：

![图片](https://img1.dotnet9.com/2024/02/0302.png)

点击上传或将文档推动到此处（暂仅支持 md 或 txt 等文本文件后续会支持 pdf 等格式）

![图片](https://img1.dotnet9.com/2024/02/0303.png)

上传我们的文档下面提供我们的文档模板：

```markdown
# 为什么选择 MASA Blazor？

## Blazor 是什么？

Blazor 是一个使用 .NET 生成交互式客户端 Web UI 的框架：

- 使用 C# 代替 JavaScript 来创建信息丰富的交互式 UI。
- 共享使用 .NET 编写的服务器端和客户端应用逻辑。
- 将 UI 呈现为 HTML 和 CSS，以支持众多浏览器，其中包括移动浏览器。
- 与新式托管平台（如 Docker）集成。
  使用 .NET 进行客户端 Web 开发可提供以下优势：
- 使用 C# 代替 JavaScript 来编写代码。
- 利用现有的 .NET 库生态系统。
- 在服务器和客户端之间共享应用逻辑。
- 受益于 .NET 的性能、可靠性和安全性。
- 在 Windows、Linux 和 macOS 上使用 Visual Studio 保持高效工作。
- 以一组稳定、功能丰富且易用的通用语言、框架和工具为基础来进行生成。

## MASA Blazor 是什么？

基于 Material Design 设计和 BlazorComponent 的交互能力提供标准的基础组件库。提供如布局、弹框标准、Loading、全局异常处理等标准场景的预置组件。从更多实际场景出发，满足更多用户和场景的需求，缩短开发周期，提高开发效率，并提供一整套 Web 解决方案 - MASA Blazor Pro。

## 为什么选择 MASA Blazor？

MASA Blazor 基于 Material 设计规范，每一个组件都经过精心设计，具有模块化、响应式和优秀的性能。

MASA Blazor 是由一支专业的全职技术团队进行定期维护升级，高效的响应速度，多元化的解决方案，并提供企业级支持。目前已在知名企业使用，且 MASA 团队自研的 MASA Stack 产品线也将持续使用，除了可以保证项目质量，还可以持续的增加新的组件和功能。

MASA Stack 除了为开发者提供众多中台类开源项目，其最基础的组成部分之一 MASA Blazor 也希望可以打造成最实用的组件库。

## Masa Blazor 和 Token 有什么关系？

Token 是一个 01 年的.NET 程序员，热爱开源，Token 经常对于 Masa 的开源项目进行贡献开源代码，他们的渊源就像是上天指定的一样，Masa Blazor 是一个非常不错的开源项目哦。

## 优势：

- 丰富组件：包含 Vuetify 1:1 还原的基础组件，以及很多实用的预置组件和.Net 深度集成功能，包括 Url、面包屑、导航三联动，高级搜索，i18n 等。
- UI 设计语言：设计风格现代，UI 多端体验设计优秀。
- 专业示例：MASA Blazor Pro 提供多种常见场景的预设布局。
- 简易上手：丰富详细的上手文档，免费的视频教程（制作中）。
- 社区活跃鼓励：用户参与实时互动，做出贡献加入我们，构建最开放的开源社区。
- 长期支持：全职团队维护，并提供企业级支持。
```

点击下一步数据处理：

![图片](https://img1.dotnet9.com/2024/02/0304.png)

在这里我们提供了直接拆分和问答拆分（暂未实现）

我们修改了自定义处理的参数，这个数值会影响到文档拆分，如果拆分得当回复的效果会更好，如果拆分过大会导致 token 消耗太大可能导致欠费，然后我们再点击下一步：

![图片](https://img1.dotnet9.com/2024/02/0305.png)

在这里我们可以看到所有的需要上传的文件，在这里的步骤是先将文件上传到服务器，然后会将数据添加到后台，并且进行向量化数据，这个过程会比较长具体看文档内容。

上传完成以后列表会显示上传的数据，点击查看可以查看到拆分的文档的所有数据

![图片](https://img1.dotnet9.com/2024/02/0306.png)

点击查看详情能看到所有的信息

![图片](https://img1.dotnet9.com/2024/02/0307.png)

点击应用->创建应用：

![图片](https://img1.dotnet9.com/2024/02/0308.png)

打开应用然后店家选择知识库，将刚刚添加的知识库于当前应用绑定，然后点击保存修改，，这样对话的时候就会搜索绑定的知识库了，在这里我们也可以修改一些应用参数，比如开场白或角色的 prompt 定义：

![图片](https://img1.dotnet9.com/2024/02/0309.png)

然后我们点击聊天，然后输入我们的知识库的内容

![图片](https://img1.dotnet9.com/2024/02/0310.png)

问：Masa Blazor 和 Token 有什么关系？

![图片](https://img1.dotnet9.com/2024/02/0311.png)

我们可以看到以上知识库的回复效果，如果你是直接提问 Gpt 的话就不把你知道 Token 是谁！知识库再一定情况下能弥补 AI 的欠缺，再比如定制我们的企业文档的时候只需要吧文档全部给知识库然后将应用做成一个对话我们就可以为客户提供一个体验更好的文档小助手，甚至于可以替代非常多的客服人力成本，如果你有更深的定制需求可以联系我微信：hjl010426

## **项目开源**

`FastWiki`采用 Apache-2.0，您也可以完全商用不会有任何版权纠纷

Github: https://github.com/239573049/fast-wiki Gitee: https://gitee.com/hejiale010426/fast-wiki

`FastWiki`技术交流群：

![图片](https://img1.dotnet9.com/2024/02/0312.png
