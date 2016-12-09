---
title: Testing with SQLite | Microsoft Docs
author: rowanmiller
ms.author: rowmil

ms.date: 10/27/2016

ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
ms.technology: entity-framework-core
 
uid: core/miscellaneous/testing/sqlite
---

# Testing with SQLite
# 使用 SQLite 进行测试

> [!NOTE]
> This documentation is for EF Core. For EF6.x, see [Entity Framework 6](../../../ef6/index.md).

SQLite has an in-memory mode that allows you to use SQLite to write tests against a relational database, without the overhead of actual database operations.
>SQLite 拥有一种 in-memory 模式，它允许你使用 SQLite 编写关系数据库相关的测试，而避免了实际数据库的操作开销。

> [!TIP]
> You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub
>> 你可以查看 GitHub 上的这篇文章 [示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub

## Example testing scenario
## 示例测试场景

Consider the following service that allows application code to perform some operations related to blogs. Internally it uses a `DbContext` that connects to a SQL Server database. It would be useful to swap this context to connect to an in-memory SQLite database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.
>考虑下面的服务，允许应用代码去执行一些与 blogs 相关的操作。它将对切换这个上下文连接到 in-memory 模式的 SQLite 数据库是有益的，
以便我们可以为这个服务编写高效的测试，而不需要修改代码或花费大量的工作为两种上下文创建测试。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## Get your context ready
## 准备你的上下文

### Avoid configuring two database providers
### 避免配置两个数据库提供者

In your tests you are going to externally configure the context to use the InMemory provider. If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.
>在你的测试中，你在外部配置上下文使用 InMemory 提供者。如果你是通过重载上下文中的 `OnConfiguring` 方法来配置数据库提供者，那么你需要添加一些条件代码来确保仅配置你准备好的数据库提供者。

> [!NOTE]
> If you are using ASP.NET Core, then you should not need this code since your database provider is configured outside of the context (in Startup.cs).
>> 如果你使用 ASP.NET Core，那么你应该不需要这些代码，因为你的数据库提供者是被配置在上下文外面的（在 Startup.cs 中）。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### Add a constructor for testing
### 为测试添加一个构造函数

The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.
>使测试支持不同的数据库的最简单途径是修改你的上下文来公开一个接受 `DbContextOptions<TContext>` 的构造函数。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!NOTE]
> `DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to. This is the same object that is built by running the OnConfiguring method in your context.
>> `DbContextOptions<TContext>` 记述了上下文的所有设置，比如哪个数据库被连接。这与在你的上下文 OnConfiguring 方法运行时构建的是同一个对象。

## Writing tests
## 编写测试

The key to testing with this provider is the ability to tell the context to use SQLite, and control the scope of the in-memory database. The scope of the database is controlled by opening and closing the connection. The database is scoped to the duration that the connection is open. Typically you want a clean database for each test method.
>关键是测试代码有能力告诉上下文使用 SQLite 提供者，并控制 in-memory 数据库的作用域。通过打开和关闭连接来控制数据库的作用域。
数据库在作用域期间是被打开的。通常你总是想为每个测试方法准备一个干净的数据库。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
