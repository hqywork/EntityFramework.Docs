---
title: Testing | Microsoft Docs
author: rowanmiller
ms.author: rowmil

ms.date: 10/27/2016

ms.assetid: 1603be0c-69bc-4dd9-9a08-3d0129cdc6c1
ms.technology: entity-framework-core
 
uid: core/miscellaneous/testing/index
---

# Testing
# 测试

You may want to test components using something that approximates connecting to the real database, without the overhead of actual database I/O operations.
>你可能希望使用与连接到实际数据库近似的东西来测试组件，而不用考虑实际数据库的 I/O 操作的开销。

There are two main options for doing this:
>有两种主要方式来实现这件事：

 * [SQLite in-memory mode](sqlite.md) allows you to write efficient tests against a provider that behaves like a relational database.
   
   [SQLite in-memory mode](sqlite.md) 允许对你编写的类似于关系型数据库的行为进行有效的测试。
 * [The InMemory provider](in-memory.md) is a lightweight provider that has minimal dependencies, but does not always behave like a relational database.
 
   [The InMemory provider](in-memory.md) 是一个轻量级的提供者，拥有最小化的依赖，但其行为不完全与关系型数据库一致。
