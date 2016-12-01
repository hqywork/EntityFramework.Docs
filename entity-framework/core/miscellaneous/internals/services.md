---
title: Understanding EF Services | Microsoft Docs
author: rowanmiller
ms.author: rowmil

ms.date: 10/27/2016

ms.assetid: a871e3ca-3650-459d-b084-8fde5d7b2e3a
ms.technology: entity-framework-core
 
uid: core/miscellaneous/internals/services
---
# Understanding EF Services
# 认识 EF 服务

> [!NOTE]
> This documentation is for EF Core. For EF6.x, see [Entity Framework 6](../../../ef6/index.md).

Entity Framework executes as a collection of services working together. A service is a reusable component. A service is typically an implementation of an interface. Services are available to other services via [dependency injection (DI)](https://wikipedia.org/wiki/Dependency_injection), which is implemented in EF using [Microsoft.Extensions.DependencyInjection](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html).
>Entity Framework 以正在工作的服务集合一起执行。一个服务是一个可重用的组件。一个服务通常是一个接口的实现。服务通过依赖注入提供给另外的服务，EF 使用 Microsoft.Extensions.DependencyInjection 来实现的。

This article covers some fundamentals principles for understanding how EF uses services and DI.
>本文介绍了 EF 如何使用服务和依赖注入的一些基本原则。

## Terms
## 术语

**Service**

**服务**

   A reusable component. In .NET, a service can be identified by a class or interface. By convention, Entity Framework only uses interfaces to identify services.
   >一个可重用组件。在 .NET 中，服务可以通过类或接口来标识。按照惯例，Entity Framework 仅使用接口来标识。

**Service lifetime**

**服务生命周期**

   A description of the way in which a service is persisted and disposed across multiple uses of the same service type.
   >描述了同一服务类型横跨多个用途时服务的持续及销毁方式。

**Service provider**

**服务提供者**

   The mechanism for storing a collection of services. Also known as a service container.
   >存储一个服务集合的机制。也被称为服务容器。

**Service collection**

**服务集合**

   The mechanism for constructing a service provider.
   >构建一个服务提供者的机制。

## Categories of Services
## 服务类别

Services fall into one or more categories.
>服务属于一个或多个类别

**Context services**

**上下文服务**

   Services that are related to a specific instance of  `DbContext`. They provide functionality for working with the user model and context options.
   >这些服务都与 `DbContext` 的具体实例有关。它们提供了用户模型和上下文选项相关的功能。

**Provider services**

**提供者服务**

   Provider-specific implementations of services. For example, SQLite uses "provider services" to customize the behavior of SQL generation, migrations, and file I/O.
   >用于提供服务的具体功能。例如，SQLite 使用“提供者服务”来定制 SQL 生成、迁移以及文件 I/O 等行为，

**Design-time services**

**设计时服务**

   Services used when a developer is creating an application. For example, EF commands uses design-time services to execute migrations and code generation (aka scaffolding).
   >开发者在创建一个应用程序时使用的服务。例如，EF 命令使用设计时服务来执行迁移和代码生成（scaffolding - 即从数据库生成 EF 代码）。

**User services**

**用户服务**

   A user can define custom services to interact with EF. These are written in application code, not provider code. For example, users can provide an implementation of `IModelCustomizer` for controlling how a model is created.
   >用户可以定义定制的服务来与 EF 互动。这些是在应用程序代码中收发室的，而不在提供者代码中。例如，用户为了控制如何创建模型可以提供一个 `IModelCustomizer` 的实现。

> [!NOTE]
> Service provider is not to be confused with a "provider's services".
  服务提供者不能与“提供者的服务”混淆。

## Service Lifetime
## 服务生命周期

EF services can be registered with different lifetime options. The suitable option depends on how the service is used and implemented.
>EF 服务可以被注册成不同的生命周期选项。恰当的选择取决于服务是被如何使用和实现的。

**Transient**

**瞬时**

   Transient lifetime services are created each time they are injected into other services. This isolates each instance of the service. For example, `MigrationsScaffolder` should not be reused, therefore it is registered as transient.
   >在每次注入到其它服务时瞬时生命周期服务都会被创建。这隔离了服务的每个实例。例如，`MigrationsScaffolder` 不应用被重用，因此它被注册为瞬时。

**Scoped**

**范围**

   Scoped lifetime services are created once per `DbContext` instance. This is used to isolate instance of `DbContext`. For example, `StateManager` is added as scoped because it should only track entity states for one context.
   >每一个 `DbContext` 实例中范围服务都会被创建。这被用来隔离 `DbContext` 的实例。例如，`StateManager` 是作为范围添加的，因为它应该仅为每一个上下文跟踪实体状态。

**Singleton**

**单例**

   Singleton lifetime services exists once per service provider and span all scopes. Each time the service is injected, the same instance is used. For example, `IModelCustomizer` is a singleton because it is idempotent, meaning each call to `IModelCustomizer.Customize()` does not change the customizer.
   >单例生命周期仅存在于每个服务提供者中并且跨越所有的范围。每次服务被注入，相同的实例会被使用。例如，`IModelCustomizer` 是单例，因为它是等幂的，意味着每次调用 `IModelCustomizer.Customize()` 都不能改变定制者。

## How AddDbContext works
## AddDbContext 如何工作

EF provides an extension method `AddDbContext<TContext>()` for adding using EF into a service collection. This method adds the following into a service collection:
>EF 提供了一个扩展方法 `AddDbContext<TContext>()` 来添加 EF 到一个服务集合。这个方法添加下列服务到服务集合：

* `TContext` as "scoped"

* `DbContextOptions` as a "singleton"

* `DbContextOptionsFactory<T>` as a "singleton"

`AddDbContext` does not add any context services, provider services, or design-time services to the service collection (except for [special cases](#special-cases)). DbContext constructs its own internal service provider for this.
>`AddDbContext` 不能添加任何上下文服务、提供者服务或设计时服务到服务集合（除了 [特殊情况](#special-cases)）。DbContext 为这些构建了它自己内部的服务提供者。

### Special cases
### 特殊情况

`AddDbContext` adds `DbContextOptionsFactory<T>` to the service collection AddDbContext was called on (which is used to create the "external" service provider). `DbContextOptionsFactory<T>` acts as a bridge between the external service provider and DbContext's internal service provider. If the external provider has services for `ILoggerFactory` or `IMemoryCache`, these will be added to the internal service provider.
>`AddDbContext` 添加 `DbContextOptionsFactory<T>` 到服务集合，AddDbContext 被调用（这被用来创建“外部”服务提供者）。`DbContextOptionsFactory<T>` 作为外部服务提供者和 DbContext 的内部服务提供者之间的桥梁。如果外部服务提供者拥有像 `ILoggerFactory` 或 `IMemoryCache` 这样的服务，那么它们将被添加到内部服务提供者中。

The bridging is done for these common scenarios so users can easily configure logging and memory caching without needing to provide a custom internal service provider.
>桥梁被用在一些公共的场景，像用户可以轻松的配置日志和内存缓存而不需要提供定制的内部服务提供者。

## DbContext's internal service provider
## DbContext 的内部服务提供者

By default, `DbContext` uses an internal service provider that is **separate** from all other service providers in the application. This internal provider is constructed from an instance of `DbContextOptions`. Methods such as `UseSqlServer()` extend the construction step add specialized services for their database system.
>默认，`DbContext` 使用一个内部服务提供者，与应用程序中的所有其它服务提供者分离。这个内部提供者是被一个 `DbContextOptions` 的实例构建的。像 `UseSqlServer()` 等扩展方法的步骤中为它们的数据库系统添加了特定服务。

### Providing a custom internal service provider
### 提供一个定制的内部服务提供者

`DbContextOptionsBuilder` provides an API for giving a custom service provider to DbContext for EF to use internally. This API is `DbContextOptions.UseInternalServiceProvider(IServiceProvider provider)`.
>`DbContextOptionsBuilder` 提供了一个 API，传递一个定制的服务提供者给 DbContext 以便 EF 内部使用。这个 API 是 `DbContextOptions.UseInternalServiceProvider(IServiceProvider provider)`。

If a custom service provider is provided, DbContext will not use `DbContextOptions` to create its own internal service provider. The custom service provider must already have provider-specific services added.
>如果提供了一个自定义服务提供者，DbContext 将不使用 `DbContextOptions` 来创建它自己的内部服务提供者。自定义服务提供者必须已经添加了提供者特定的服务。

Database provider writers should provided methods such as AddEntityFrameworkSqlServer" or "AddEntityFrameworkSqlite" to simplify the process of creating a custom service container.
>数据库提供者的编写应该提供像 “AddEntityFrameworkSqlServer”或“AddEntityFrameworkSqlite”一样的方法，以便简化创建自定义服务容器的过程。

<!-- literal_block"language": "csharp",rp", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````csharp
var services = new ServiceCollection()
    .AddEntityFrameworkSqlServer()
    .AddSingleton<MyCustomService>()
    .BuildServiceProvider();

var options = new DbContextOptionsBuilder();

options
    .UseInternalServiceProvider(services)
    .UseSqlServer(connectionString);

using (var context = new DbContext(options))
{ }
````

### Service provider caching
### 服务提供者缓存

EF caches this internal service provider with `IDbContextOptions` as the key. This means the service provider is only created once per unique set of options. It is reused when a DbContext is instantiated using a set of options that have already been used during the application lifetime.
>EF 使用 `IDbContextOptions` 作为关键字缓存这个内部服务提供者。这意味着服务提供者仅被每个唯一的选项创建一次。在应用程序生命周期期间，当一个 DbContext 使用一组选项创建时它将被重用。

## Required Provider Services
## 必须的提供者服务

EF database providers must register a basic set of services. These required services are defined as properties on `IDatabaseProviderServices`. Provider writers may need to implement some services from scratch. Others have partial or complete implementations in EF's library that can be reused.
>EF 数据库提供都必须注册一组基础服务。这些必需的服务是被作为 `IDatabaseProviderServices` 的属性定义的。提供者程序编写者可能需要从零开始实现一些服务。其它在 EF 库中的部分或完整的实现可以被重用。

For more information on required provider services, see [Writing a Database Provider](writing-a-provider.md).
>有关所需的提供者服务的更多信息，请见 [Writing a Database Provider](writing-a-provider.md)。

## Additional Information
## 补充信息

EF uses `the Microsoft.Extensions.DependencyInjection library <[https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/)>`_ to implement DI. Documentation for this library [is available on docs.asp.net](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html).
>EF 使用 `Microsoft.Extensions.DependencyInjection 库 <[https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/)>` 实现依赖注入。这个库的文档在 [docs.asp.net](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html)

[System.IServiceProvider](https://docs.microsoft.com/en-us/dotnet/core/api/system.iserviceprovider) is defined in the .NET base class library.
>[System.IServiceProvider](https://docs.microsoft.com/en-us/dotnet/core/api/system.iserviceprovider) 定义在 .NET 基础类库中。