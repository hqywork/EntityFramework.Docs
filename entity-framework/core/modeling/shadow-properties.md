---
title: Shadow Properties | Microsoft Docs
author: rowanmiller
ms.author: rowmil

ms.date: 10/27/2016

ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
ms.technology: entity-framework-core
 
uid: core/modeling/shadow-properties
---
# Shadow Properties
# 阴影属性

> [!NOTE]
> This documentation is for EF Core. For EF6.x, see [Entity Framework 6](../../ef6/index.md).

Shadow properties are properties that do not exist in your entity class. The value and state of these properties is maintained purely in the Change Tracker.
>阴影属性是在你的实体类中不存在的属性。这些属性的值和状态仅在更改跟踪中进行维护。

Shadow property values can be obtained and changed through the `ChangeTracker` API.
>阴影属性值可以通过  `ChangeTracker` API 进行获取和改变。

<!-- literal_block"language": "csharp",rp", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````csharp
   context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
````

Shadow properties can be referenced in LINQ queries via the `EF.Property` static method.
>阴影属性在 LINQ 查询中可以通过 `EF.Property` 静态方法来引用。

<!-- literal_block"language": "csharp",rp", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
````

## Conventions
## 约定

By convention, shadow properties are only created when a relationship is discovered but no foreign key property is found in the dependent entity class. In this case, a shadow foreign key property will be introduced. The shadow foreign key property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming). If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`. If there is no navigation property on the dependent entity, then the principal type name is used in its place.
>按照约定，阴影属性仅在依赖实体类的关系被发现但无法找到外键属性时才会被创建。在这种情况下，阴影外键属性将被引入。
阴影外键属性将被命名为 `<navigation property name><principal key property name>` (依赖实体上的导航属性以及它指向的主要实体，是被命名使用的)。
如果主要实体键属性名称包含了导航属性的名称，那么这个名称正好是 `<principal key property name>`。如果在依赖实体上没有导航属性，那么主要类型名称将被在其位置中使用。

For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity.
>例如，下面的代码将导致 `BlogId`  阴影属性被引入到 `Post` 实体。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/ShadowForeignKey.cs)] -->
````csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
````

## Data Annotations
## 数据注解

Shadow properties can not be created with data annotations.
阴影属性不可以通过数据注解来创建。

## Fluent API

You can use the Fluent API to configure shadow properties. Once you have called the string overload of `Property` you can chain any of the configuration calls you would for other properties.
>你可以使用 Fluent API 来配置阴影属性。一旦你调用了 `Property` 方法的字符串重载，你将可以为想要的属性以链式方式调用任何的配置。

If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.
>如果提供给 `Property` 方法的名称与已存在的属性一致（阴影属性或实体类上定义的属性），那么代码将配置已存在的属性而不是引入新的阴影属性。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/ShadowProperty.cs?highlight=7,8)] -->
````csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property<DateTime>("LastUpdated");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
````
