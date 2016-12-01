---
title: Testing with InMemory | Microsoft Docs
author: rowanmiller
ms.author: rowmil

ms.date: 10/27/2016

ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
ms.technology: entity-framework-core
 
uid: core/miscellaneous/testing/in-memory
---

# Testing with InMemory
# 使用 InMemory 进行测试

> [!NOTE]
> This documentation is for EF Core. For EF6.x, see [Entity Framework 6](../../../ef6/index.md).

The InMemory provider is useful when you want to test components using something that approximates connecting to the real database, without the overhead of actual database operations.
>当你想与连接到实际数据库近似的东西来测试组件时，InMemory 提供者对你是有帮助的，而不用考虑实际数据库的 I/O 操作的开销。

> [!TIP]
> You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub.

> 你可以查看 GitHub 上的文章 [示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)

## InMemory is not a relational database
## InMemory 不是一个关系型数据库

EF Core database providers do not have to be relational databases. InMemory is designed to be a general purpose database for testing, and is not designed to mimic a relational database.
>EF Core 数据库提供者不一定是关系型数据库。InMemory 是以测试为目的被设计出来的通用数据库，而不是模拟一个关系型数据库。

Some examples of this include:
>这包含了一些示例：

* InMemory will allow you to save data that would violate referential integrity constraints in a relational database.

  在关系型数据库中违反引用完整性约束的数据，InMemory 将允许你保存。

* If you use DefaultValueSql(string) for a property in your model, this is a relational database API and will have no effect when running against InMemory.

  如果你在模型中的属性中使用了 DefaultValueSql(string)，这是一个关系型数据库 API，当运行在 InMemory 时将没有任何效果。

> [!TIP]
> For many test purposes these difference will not matter. However, if you want to test against something that behaves more like a true relational database, then consider using [SQLite in-memory mode](sqlite.md).

> 对于多数测试的目的来说，这些差异是无足轻重的。然而，如果你想测试行为更像一个真实的关系型数据库一样的东西，那么请考虑使用 [SQLite in-memory mode](sqlite.md)。

## Example testing scenario
## 测试场景示例

Consider the following service that allows application code to perform some operations related to blogs. Internally it uses a `DbContext` that connects to a SQL Server database. It would be useful to swap this context to connect to an InMemory database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.
> 思考下面的服务，它允许应用程序代码执行一些与 blogs 相关的操作。内部它使用一个 `DbContext` 链接到一个 SQL Server 数据库。这有助于我们切换这个上下文连接到 InMemory 数据库，以便我们可以为这个服务编写有效的测试，而不需要修改代码或增加工作量来创建双倍的测试。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## Get your context ready
## 准备上下文

### Avoid configuring two database providers
### 避免配置两个数据库提供者

In your tests you are going to externally configure the context to use the InMemory provider. If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.
>在你的测试中，你是通过外部配置上下文来使用 InMemory 提供者的。如果你是通过在上下文中重写 `OnConfiguring` 方法来配置数据库提供者，那么你需要添加一些条件代码来确保仅在未配置时配置数据库提供者。

> [!NOTE]
> If you are using ASP.NET Core, then you should not need this code since your database provider is configured outside of the context (in Startup.cs).

> 如果你使用 ASP.NET Core，那么你不应用需要这些代码，因为你的数据库提供者是在上下文外部（在 Startup.cs 中）配置的。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### Add a constructor for testing
### 为每个测试方法添加一个构建函数

The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.
>为了使用测试可以针对不同的数据库，最简单的途径是修改你的上下文，公开一个构造函数来接受 `DbContextOptions<TContext>`。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!NOTE]
> `DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to. This is the same object that is built by running the OnConfiguring method in your context.

> `DbContextOptions<TContext>` 告诉上下文它所有的设置，如连接到哪个数据库。这与在运行中你的上下文 OnConfiguring 方法所构建的是同一对象。

## Writing tests
## 编写测试

The key to testing with this provider is the ability to tell the context to use the InMemory provider, and control the scope of the in-memory database. Typically you want a clean database for each test method.
>使用这个提供者测试的关键是有能力告诉上下文要使用 InMemory 提供者并控制 in-memory 数据库的范围。通常，你希望为每个测试方法准备一个干净的数据库。

Here is an example of a test class that uses the InMemory database. Each test method specifies a unique database name, meaning each method has its own InMemory database.
>这是一个使用 InMemory 数据库的测试类的示例。每个测试方法指定一个唯一的数据库名称，意味着每个方法都拥有自已的 ImMemory 数据库。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
