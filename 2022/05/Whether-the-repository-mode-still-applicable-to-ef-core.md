---
title: 仓储模式是否依然适用于EF Core？
slug: Whether-the-repository-mode-still-applicable-to-ef-core
description: 昨天『.NET 大牛之路』群里的小伙伴们谈论了使用 EF Core 实现仓储模式的话题，我想起以前看过一篇一名国外大佬写的文章，觉得非常有参考价值。
date: 2022-05-04 16:04:32
copyright: Reprinted
author: liamwang 精致码农
originaltitle: 仓储模式是否依然适用于EF Core？
originallink: https://mp.weixin.qq.com/s/DyzREfZbtZVOQ5MbIQ46cg
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_09.png
categories: .NET
tags: .NET,ORM,EF Core
---

昨天『.NET 大牛之路』群里的小伙伴们谈论了使用 EF Core 实现仓储模式的话题，我想起以前看过一篇一名国外大佬写的文章，觉得非常有参考价值，今天进行了翻译，供大家赏鉴。

>原文：https://www.thereformedprogrammer.net/is-the-repository-pattern-useful-with-entity-framework-core/
>
>作者：Jon P Smith
>
>翻译：精致码农-王亮
>
>说明：原文首次发布于 2018 年 2 月，最后更新于 2020 年 7 月。

正文：

我在 2014 年写了第一篇关于仓储模式的文章，它仍然是一篇很受欢迎的文章。而这一篇文章是那篇文章的更新版，基于这几年 EF Core 新的发布和对 EF Core 数据库访问模式的进一步研究。

1. Original: Analysing whether Repository pattern useful with Entity Framework (2014年5月).

   https://www.thereformedprogrammer.net/is-the-repository-pattern-useful-with-entity-framework/

2. First solution: Four months on – my solution to replacing the Repository pattern (2014年10月).

   https://www.thereformedprogrammer.net/is-the-repository-pattern-useful-with-entity-framework-part-2/

3. THIS ARTICLE: Is the repository pattern useful with Entity Framework Core?

## 1 概要

答案是“否”，仓储/工作单元模式（简称 Rep/UoW）对 EF Core 没有用。EF Core 已经实现了 Rep/UoW 模式，所以在 EF Core 上再加一个 Rep/UoW 模式是没有用的。

更好的解决方案是直接使用 EF Core，它允许你使用 EF Core 的所有功能来构建高性能的数据库访问。

## 2 本文目的

这篇文章关注的是：

- 人们对 EF 的 Rep/UoW 模式有什么看法。

- 在 EF 中使用 Rep/UoW 模式的优点和缺点。

- 用 EF Core 代码取代 Rep/UoW 模式的三种方法。

- 如何使你的 EF Core 数据库访问代码易于发现和重构。

- 关于 EF Core 单元测试的讨论。

我将假设你熟悉 C# 和 EF 6.x 或 EF Core 库。文中我特别谈到了 EF Core，但大部分内容也与 EF6.x 有关。

## 3 背景

在 2013 年，我参与了一个专门用于医疗保健建模的大型网络应用程序的开发工作。我使用了 ASP.NET MVC4 和 EF 5，后者当时刚刚问世，支持处理地理数据的 SQL Spatial 类型。当时流行的数据库访问模式是 Rep/UoW 模式--参见微软在 2013 年写的关于使用 EF Core 和 Rep/UoW 模式访问数据库的文章。

- Implementing the Repository and Unit of Work Patterns in an ASP.NET MVC Application

https://docs.microsoft.com/en-us/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application

我使用 Rep/UoW 构建了我的应用程序，但在开发过程中发现这确实是一个痛点。我不得不不断地“调整”版本库的代码来修复一些小问题，而每次“调整”都会破坏一些其他的东西。正是这一点让我研究如何更好地实现我的数据库访问代码。

说到这里，我在 2017 年底与一家新成立的公司签约，帮助他们解决 EF6.x 应用程序的性能问题。性能问题的主要部分被证明是由于懒加载，这是需要的，因为应用程序使用了 Rep/UoW 模式。

事实证明，一个参与启动项目的程序员曾经使用过 Rep/UoW 模式，在与该公司的创始人交谈时，他说他发现应用程序的 Rep/UoW 部分是相当不透明的，很难操作。

## 4 人们如何看仓储模式

在研究 Spatial Modeller™ 设计的过程中，我发现了一些博客文章，为抛弃仓储模式提供了有力的证据。这类文章中最有说服力、考虑最周全的是“Repositories On Top UnitOfWork Are Not a Good Idea”。Rob Conery 的主要观点是，Rep/UoW 只是重复了 Entity Framework (EF) DbContext 给你的东西，所以为什么要把一个完美的框架隐藏在一个没有任何价值的外表下呢。Rob 称之为“过度抽象的愚蠢行为”。

另一篇博客是“Why Entity Framework renders the Repository pattern obsolete”。在这篇文章中，Isaac Abraham 补充说，仓储模式并没有使测试变得更容易，这是它本应该做的一件事。这一点在 EF Core 中更加实际，你将在后面看到。

那么，他们是对的吗？

## 5 我对 Rep/UoW 模式的看法

让我试着以尽可能公平的方式回顾一下 Rep/UoW 模式的优点和缺点。以下是我的观点。

### 5.1  Rep/UoW 模式的优点

1. 隔离你的数据库访问代码。仓库模式的最大优点是你知道你所有的数据库访问代码在哪里。另外，你通常会把你的仓储库分成几个部分，如目录库、订单处理库等，这使得你很容易找到有错误或需要性能调整的特定查询的代码。这无疑是一个很大的优点。

2. 聚合（Aggregation）。领域驱动设计（DDD）是一种设计系统的方法，它建议你有一个根实体，其他相关的实体被归入它。我在《Entity Framework Core in Action》一书中使用的例子是一个带有 Review 实体集合的 Book 实体。这些 Review 只有在与 Book 相联系时才有意义，所以 DDD 说你应该只通过 Book 实体来改变 Review。Rep/UoW 模式通过提供一种方法在 Book Repository 中将评论添加/删除中来实现这一点。

3. 隐藏复杂的 T-SQL 命令。有时你需要绕过 EF Core，使用 T-SQL。这种类型的访问应该从高层隐藏起来，但又容易找到，以帮助维护或重构。我应该指出，Rob Conery 的帖子 Command/Query Objects 也可以处理这个问题。

4. 易于模拟/测试。很容易模拟一个单独的资源库，这使得访问数据库的单元测试代码更容易。这在若干年前是真的，但现在这有其他的方法来解决这个问题，我将在后面介绍。

你会注意到，我没有提出“用另一个数据库访问库替换 EF Core”。这是 Rep/UoW 背后的想法之一，但我认为这是一个误解，因为 a）很难替换一个数据库访问库，b）你真的会在你的应用程序中交换这样一个关键库吗？

### 5.2 Rep/UoW 模式的缺点

前三个项目都是围绕着性能。我并不是说你不能写一个高效的 Rep/UoW，但它是一项艰难的工作，而且我看到许多实现都有内在的性能问题（包括微软旧的 Rep/UoW 实现）。以下是我在 Rep/UoW 模式中发现的缺点清单。

1. 性能--处理实体关系。一个资源库通常会返回一种类型的 `IEnumerable/IQueryable` 结果，例如在微软的例子中的一个 Student 实体类。假设你想从 Student 的关系中显示信息，比如他们的地址？在这种情况下，仓储库中最简单的方法是使用懒加载来读取学生的地址实体，我看到人们经常这样做。问题是懒加载会导致每一个关系都要单独往返于数据库，这比把所有的数据库访问合并成一次数据库往返要慢。(另一种方法是有多个不同返回类型的查询方法，但这将使你的资源库变得非常大和麻烦--见第 4 点）。

2. 数据不符合要求的格式。因为仓储组件通常是根据数据库创建的，返回的数据可能不是服务或用户需要的确切格式。你也许可以调整仓储库的输出，但这是你必须要写的第二个阶段。我认为更好的做法是在靠近前端的地方形成你的查询，并包括你需要的数据的任何调整。

3. 性能--更新：许多 Rep/UoW 的实现试图隐藏 EF Core，但这样做并没有利用它的所有功能。例如，Rep/UoW 会使用 EF Core 的 Update 方法更新一个实体，该方法会保存实体中的每个属性。而使用 EF Core 内置的变化跟踪功能，它将只更新已经改变的属性。

4. 太通用了。Rep/UoW 的诱惑力来自于你可以写一个通用的仓储库（Repository），然后用它来建立所有的子仓储库，例如目录库、订单处理库等。这应该可以最大限度地减少你需要写的代码，但我的经验是，一个通用的仓储库在开始时是有效的，但随着事情变得越来越复杂，你最终不得不为每个单独的仓储库添加越来越多的代码。“The more reusable the code is, the less usable it is.” --Neil Ford

**总结一下不好的地方--Rep/UoW 隐藏了 EF Core，这意味着你不能使用 EF Core 的功能来编写简单但高效的数据库访问代码。**

## 6 如何保留 Rep/UoW 的优点使用 EF Core

在前面的优点部分中，我列出了隔离、聚合、隐藏和单元测试，Rep/UoW 做得很好。在这一节中，我将谈论一些不同的软件模式和实践，当你直接使用 EF Core 时，这些模式和实践与良好的架构设计相结合，提供同样的隔离、聚合等功能。

我将解释每一个优点的实现，然后把它们放到一个分层的软件架构中。

1. 查询对象：一种隔离和隐藏数据库读取的方法

数据库访问可分为四种类型。新增、读取、更新和删除--被称为 CRUD。对我来说，读的部分，在 EF Core 中被称为查询，往往是最难建立和性能调整的。许多应用程序都依赖于良好的、快速的查询，例如，要购买的产品列表，要做的事情列表，等等。人们想出的方案是查询对象。

我第一次接触到它们是在 2013 年 Rob Conery 的文章中（前面提到），他提到了命令/查询对象。另外，Jimmy Bogard 在 2012 年发表了一篇名为“Favor query objects over repositories”的文章。使用 .NET 的 IQueryable 类型和扩展方法，我们可以在 Rob 和 Jimmy 的例子中改进查询对象模式。

下面的列表给出了一个查询对象的简单例子，它可以选择一个整数列表的排序方式。

```csharp
public static class MyLinqExtension
{
    public static IQueryable<int> MyOrder
        (this IQueryable<int> queryable, bool ascending)
    {
        return ascending
            ? queryable.OrderBy(num => num)
            : queryable.OrderByDescending(num => num);
    }
}
```

下面这是这个 `MyOrder` 查询对象使用示例：

```csharp
var numsQ = new[] { 1, 5, 4, 2, 3 }.AsQueryable();

var result = numsQ
    .MyOrder(true)
    .Where(x => x > 3)
    .ToArray();
```

`MyOrder` 查询对象的工作原理是，`IQueryable` 类型持有一个命令列表，当我应用 `ToArray` 方法时，这些命令会被执行。在我的简单例子中，我没有使用数据库，但如果我们用应用程序的 `DbContext` 的 `DbSet<T>` 属性替换 `numsQ` 变量，那么 `IQueryable<T>` 类型中的命令将被转换为数据库命令。

因为 `IQueryable<T>` 类型直到最后才被执行，所以你可以将多个查询对象连锁起来。让我从我的书《Entity Framework Core in Action》中给你一个更复杂的数据库查询的例子。在下面的代码中，使用了四个查询对象链在一起，对一些图书的数据进行选择、排序、过滤和分页。你可以在实时网站 `efcoreinaction.com` 看到这些。

```csharp
public IQueryable<BookListDto> SortFilterPage
    (SortFilterPageOptions options)
{
    var booksQuery = _context.Books
        .AsNoTracking()
        .MapBookToDto()
        .OrderBooksBy(options.OrderByOptions)
        .FilterBooksBy(options.FilterBy,
                       options.FilterValue);

    options.SetupRestOfDto(booksQuery);

    return booksQuery.Page(options.PageNum-1,
                           options.PageSize);
}
```

查询对象提供了比 Rep/UoW 模式更好的隔离性，因为你可以把复杂的查询分割成一系列的查询对象，并把它们连在一起。这使得它更容易编写和理解、重构和测试。另外，如果你有一个需要原始 SQL 的查询，你可以使用 EF Core 的 FromSql 方法，它也可以返回 `IQueryable<T>`。

2. 新增、更新和删除数据库访问方法

查询对象处理了 CRUD 的读取部分，但是新增、更新和删除部分呢，也就是你向数据库写入的部分？我将向你展示运行 CUD 操作的两种方法：直接使用 EF Core 命令，以及使用实体类中的 DDD 方法。让我们来看看非常简单的更新例子：在我的图书应用中添加一个评论（见`efcoreinaction.com`）。

>注：如果你想尝试添加评论，有一个与我的书配套的 GitHub repo（`github.com/JonPSmith/EfCoreInAction`）,选择分支 Chapter05（每章都有一个分支）并在本地运行该应用程序。你会看到每本书旁边都有一个管理按钮，有几个 CUD 命令。

**方式一：直接使用 EF Core 命令**

最明显的方法是使用 EF Core 方法来完成数据库的更新。下面是一个方法，它将为一本书添加一个新的评论，其中包括用户提供的评论信息。注意：`ReviewDto` 是一个持有用户填写完评论信息后返回的信息类。

```csharp
public Book AddReviewToBook(ReviewDto dto)
{
    var book = _context.Books
        .Include(r => r.Reviews)
        .Single(k => k.BookId == dto.BookId);
    var newReview = new Review(dto.numStars, dto.comment, dto.voterName);
    book.Reviews.Add(newReview);
    _context.SaveChanges();
    return book;
}
```

>`注：AddReviewToBook` 方法是在一个叫做 `AddReviewService` 的类中，这个类在我的 `ServiceLayer` 中。这个类被注册为一个服务，并且有一个构造函数，它接收应用程序的 `DbContext`，这个 `DbContext` 是通过 DI 注入的。注入的值被存储在私有字段 `_context` 中，`AddReviewToBook` 方法可以使用它来访问数据库。

这将把新的评论添加到数据库中，这很有效，但还有另一种方法可以使用更多的 DDD 方法来构建。

**方式二：DDD 风格的实体类**

EF Core 为我们提供了一个新的地方来编写你的更新代码--实体类内部。EF Core 有一个叫做**后援字段**(backing fields)的功能，它使构建 DDD 实体成为可能。后援字段允许你控制对任何关系结构的访问。这在 EF6.x 中其实是不可能的。

DDD 谈到了聚合（前面提到过），所有的聚合只能通过根实体中的方法来改变，我把它称为**访问方法**。在 DDD 术语中，评论是图书实体的聚合，所以我们应该通过图书实体类中的一个名为 `AddReview` 的访问方法来添加一个评论。这样一来，上面的代码就变成了 `Book` 实体中的一个方法，在这里：

```csharp
public Book AddReviewToBook(ReviewDto dto)
{
    var book = _context.Find<Book>(dto.BookId);
    book.AddReview(dto.numStars, dto.comment,
         dto.voterName, _context);
    _context.SaveChanges();
    return book;
}
```

`Book` 实体类中的 `AddReview` 访问方法看起来像这样：

```csharp
public class Book
{
    private HashSet<Review> _reviews;
    public IEnumerable<Review> Reviews => _reviews?.ToList();
    //...other properties left out

    //...constructors left out

    public void AddReview(int numStars, string comment,
        string voterName, DbContext context = null)
    {
        if (_reviews != null)
        {
            _reviews.Add(new Review(numStars, comment, voterName));
        }
        else if (context == null)
        {
            throw new ArgumentNullException(nameof(context),
                "You must provide a context if the Reviews collection isn't valid.");
        }
        else if (context.Entry(this).IsKeySet)
        {
            context.Add(new Review(numStars, comment, voterName, BookId));
        }
        else
        {
            throw new InvalidOperationException("Could not add a new review.");
        }
    }
    //...
}
```

这个方法更复杂，因为它可以处理两种不同的情况：一种是已经加载了 `Review`，另一种是还没有。但如果评论还没有被加载，它比原来的情况要快，因为它使用了“通过外键创建关系”的方法。

因为访问方法的代码在实体类里面，如果需要的话可以更复杂，因为它将是你需要写的唯一版本的代码。在方式一中，你可以在不同的地方重复相同的代码，只要你需要更新 `Book` 的 `Review` 集合。

>注：我写了一篇名为“Creating Domain-Driven Design entity classes with Entity Framework Core”的文章，全部都是关于 DDD 风格的实体类。这篇文章对这个话题有更详细的介绍。我还更新了关于如何用 EF Core 编写业务逻辑的文章，以使用同样的 DDD 风格的实体类。

为什么实体类中的方法不调用 `SaveChanges`？在方式一中，一个方法包含了所有的部分：a）加载实体，b）更新实体，c）调用 `SaveChanges` 来更新数据库。我可以这样做，因为我知道它是由一个网络请求调用的，而这就是我想做的全部。对于 DDD 实体方法，你不能在实体方法中调用 `SaveChanges`，因为你不能确定操作已经完成。例如，如果你从备份中加载一本书，你可能想创建这本书，添加作者，添加任何评论，然后调用 `SaveChanges`，这样所有的东西都在一起被提交到数据库。

**方式三：GenericServices 库**

还有第三种方式。我注意到在我构建的 ASP.NET 应用程序中使用 CRUD 命令时有一个标准模式，早在 2014 年我就建立了一个名为 `GenericServices` 的库，它可以与 EF6.x 一起使用。2018 年我为 EF Core 建立了一个更全面的版本，名为 `EfCore.GenericServices`，见这篇关于 `EfCore.GenericServices` 的文章：

- GenericServices: A library to provide CRUD front-end services from a EF Core database

https://www.thereformedprogrammer.net/genericservices-a-library-to-provide-crud-front-end-services-from-a-ef-core-database/

这些库并没有真正实现仓储模式，而是在实体类和前端需要实际数据之间充当适配器模式。我曾使用过原始的 EF6.x，`GenericServices` 为我节省了几个月的枯燥的前端代码编写。新的 `EfCore.GenericServices` 甚至更好，因为它可以与标准风格的实体类和 DDD 风格的实体类一起工作。

3. 哪一个方式更好

方式一（直接使用 EF Core 代码）要写的代码最少，但有可能出现重复，因为应用程序的不同部分可能要对一个实体应用 CUD 命令。例如，当用户通过改变事物时，你可能会通过 `ServiceLayer` 进行更新，但外部 API 可能不会通过 `ServiceLayer`，所以你必须重复 CUD 代码。

方式二（DDD 风格的实体类）将关键的更新部分放在实体类内，所以代码对任何能得到实体实例的人都是可用的。事实上，由于 DDD 风格的实体类“锁定”了对属性和集合的访问，每个人都必须使用 `Book` 实体的 `AddReview` 访问方法，如果他们想更新 `Review` 集合的话。出于许多原因，这是我想在未来的应用中使用的方法（见我的文章中关于利弊的讨论）。其（轻微的）缺点是它需要一个单独的加载/保存部分，这意味着更多的代码。

方式三（GenericServices 库）是我的首选方法，尤其是现在我已经建立了 `EfCore.GenericServices` 版本，可以处理 DDD 风格的实体类。正如你在关于 `EfCore.GenericServices` 的文章中所看到的，这个库极大地减少了你在 Web/移动/桌面应用程序中需要编写的代码。当然，你仍然需要在你的业务逻辑中访问数据库，但这是另一回事。

## 7 组织你的 CRUD 代码

Rep/UoW 模式的一个好处是，它将你所有的数据访问代码放在一个地方。当换成直接使用 EF Core 时，你可以把你的数据访问代码放在任何地方，但这使得你或其他团队成员很难找到它。因此，我建议对你的代码放在哪里有一个明确的计划，并坚持下去。

下图展示了一个分层或六边形的架构，只展示了三个程序集（我省去了业务逻辑，在六边形的架构中，你会有更多的程序集）。显示的三个程序集是：

- ASP.NET Core。这是表现层，提供 HTML 页面或一个网络 API。这没有数据库访问代码，但依赖于 ServiceLayer 和 BusinessLayer 中的各种方法。

- 服务层。它包含数据库访问代码，包括查询对象和新增、更新和删除方法。服务层使用适配器模式和命令模式来连接数据层和 ASP.NET Core（表现）层。

- 数据层。它包含了应用程序的 DbContext 和实体类。然后，DDD 风格的实体类包含访问方法，以允许根实体及其聚合体被修改。

![](https://img1.dotnet9.com/2022/05/0901.png)

>注：前面提到的库 `GenericServices`（EF6.x）和 `EfCore.GenericServices`（EF Core）实际上是一个提供 `ServiceLayer` 功能的库，即在 `DataLayer` 和你的 Web/移动/桌面应用程序之间充当适配器模式和命令模式。

从这个图中我想说的是，通过使用不同的程序集，一个简单的命名标准（见图中黑体字 `Book`）和文件夹，你可以建立一个应用程序，其中你的数据库代码是独立的，很容易找到。随着你的应用程序的增长，这可能是至关重要的。

## 8 EF Core 方法单元测试

最后要看的部分是对使用 EF Core 的应用程序进行单元测试。仓储模式的优点之一是你可以在测试时用一个模拟来代替它。因此，直接使用 EF Core 就失去了模拟的选择（技术上你可以模拟 EF Core，但很难做得好）。

值得庆幸的是，现在的 EF Core 已经有了进步，你可以用内存数据库来模拟数据库了。内存数据库的创建速度更快，而且有一个默认的起始点（即，空），所以针对它编写测试要容易得多。参见我的文章“Using in-memory databases for unit testing EF Core applications” 详细了解如何做到这一点，另外还有一个名为 EfCore.TestSupport 的 NuGet 包，它提供了一些方法，使编写 EF Core 单元测试更加快速。

## 9 结论

我上一个使用 Rep/UoW 模式的项目要追溯到 2013 年，从那以后我再也没有使用过 Rep/UoW 模式。我尝试过一些方法，一个基于 EF6.x 名为 `GenericServices` 的自定义库，以及现在一个更标准的基于 EF Core 实现查询对象和 DDD 风格的实体类方法的 `EfCore.GenericServices` 自定义库。它们使得编写代码更容易，而且通常表现良好。但如果它们很慢，就很容易定位并对单个数据库访问进行性能调整。

在我为 Manning 出版社写的书中，有一章是对一个“卖”书的 ASP.NET Core 应用程序进行性能调整。这个过程使用了查询对象和 DDD 实体方法，并表明它可以产生性能很好的数据库访问（见我的文章“ Entity Framework Core performance tuning – a worked example”）。

我自己的工作是使用查询对象进行读取，并使用 DDD 风格的实体类及其 CUD 和业务逻辑的访问方法。我确实需要在一个适当的应用中使用这些东西，才能真正知道它们是否有效。请关注我的博客，了解更多关于 DDD 风格的实体类，以及从中受益的架构，也许还会有一个新的库:)。

编码愉快！