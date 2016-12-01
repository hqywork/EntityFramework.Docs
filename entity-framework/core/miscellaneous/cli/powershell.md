---
title: Package Manager Console (Visual Studio) | Microsoft Docs
author: rowanmiller
ms.author: rowmil

ms.date: 10/27/2016

ms.assetid: c02a68d0-187a-47fb-af80-031f4187ad8a
ms.technology: entity-framework-core

uid: core/miscellaneous/cli/powershell
---
# Package Manager Console (Visual Studio)
# 包管理控制台（Visual Studio）

> [!NOTE]
> This documentation is for EF Core. For EF6.x, see [Entity Framework 6](../../../ef6/index.md).

EF command line tools for Visual Studio's Package Manager Console (PMC) window.
>运用于 Visual Studio 包管理控制台（PMC）窗口的 EF 命令行工具。

> [!WARNING]
> The commands require the [latest version of Windows PowerShell](https://www.microsoft.com/en-us/download/details.aspx?id=50395)
>>要求安装最新版本的 [Windows PowerShell](https://www.microsoft.com/en-us/download/details.aspx?id=50395)

## Installation
## 安装

Package Manager Console commands are installed with the *Microsoft.EntityFrameworkCore.Tools* package.
>使用包管理器控制台命令安装 *Microsoft.EntityFrameworkCore.Tools* 包。

To open the console, follow these steps.
>使用下列的步骤打开控制台。

* Open Visual Studio 2015

打开 Visual Studio 2015

* Tools ‣ Nuget Package Manager ‣ Package Manager Console

工具 ‣ Nuget 包管理器 ‣ 包管理控制台

* Execute `Install-Package Microsoft.EntityFrameworkCore.Tools -Pre`

执行 `Install-Package Microsoft.EntityFrameworkCore.Tools -Pre`

### .NET Core and ASP.NET Core Projects
### .NET Core 和 ASP.NET Core 项目

.NET Core and ASP.NET Core projects also require installing .NET Core CLI. See [.NET Core CLI](dotnet.md) for more information about this installation.
>.NET Core 和 ASP.NET Core 项目还必须安装 .NET Core CLI。关于这些安装的更多信息请参考 [.NET Core CLI](dotnet.md)。

> [!NOTE]
> .NET Core CLI has known issues in Preview 1. Because PMC commands call .NET Core CLI commands, these known issues also apply to PMC commands. See [Preview 2 Known Issues](dotnet.md).
>> .NET Core CLI 在预览版 2 中有一些已知问题。由于 PMC 命令调用了 .NET Core CLI 命令，因此这些已知问题同样适用于 PMC 命令。请参考 [预览版 2 已知问题](dotnet.md).

> [!TIP]
> On .NET Core and ASP.NET Core projects, add `-Verbose` to any Package Manager Console command to see the equivalent .NET Core CLI command that was invoked.
>>在 .NET Core 和 ASP.NET Core 项目上，为任一包管理器控制台命令添加 `-Verbose` 参数，等效 .NET Core CLI 命令被调用。


## Usage
## 用法

> [!NOTE]
> All commands support the common parameters: `-Verbose`, `-Debug`, `-ErrorAction`, `-ErrorVariable`, `-WarningAction`, `-WarningVariable`, `-OutBuffer`, `-PipelineVariable`, and `-OutVariable`. For more information, see [about_CommonParameters](http://go.microsoft.com/fwlink/?LinkID=113216).
>>所有命令都支持的公共参数：`-Verbose`, `-Debug`, `-ErrorAction`, `-ErrorVariable`, `-WarningAction`, `-WarningVariable`, `-OutBuffer`, `-PipelineVariable`, 以及 `-OutVariable`。请参考 [about_CommonParameters](http://go.microsoft.com/fwlink/?LinkID=113216)。

### Add-Migration

Adds a new migration.
>添加一个新迁移。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````text
SYNTAX
    Add-Migration [-Name] <String> [-OutputDir <String>] [-Context <String>] [-Project <String>]
     [-StartupProject <String>] [-Environment <String>] [<CommonParameters>]

PARAMETERS
    -Name <String>
        Specifies the name of the migration.
        指定迁移的名称。

    -OutputDir <String>
        The directory (and sub-namespace) to use. If omitted, "Migrations" is used. Relative paths are relative to project directory.
        要使用的目录（即子命令空间）。如果省略，将使用“Migrations”。相对路径是相对于项目目录。

    -Context <String>
        Specifies the DbContext to use. If omitted, the default DbContext is used.
        指定要使用的 DbContext。如果省略，默认 DbContext 将被使用。

    -Project <String>
        Specifies the project to use. If omitted, the default project is used.
        指定要使用的项目。如果省略，默认项目将被使用。

    -StartupProject <String>
        Specifies the startup project to use. If omitted, the solution's startup project is used.
        指定要使用的启动项目。如果省略，解决方案中的启动项目将被使用。

    -Environment <String>
        Specifies the environment to use. If omitted, "Development" is used.
        指定要使用的环境。如果省略，将使用“Development”。
````

### Remove-Migration

Removes the last migration.
>移除最新的迁移。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````text
SYNTAX
    Remove-Migration [-Context <String>] [-Project <String>] [-StartupProject <String>] [-Environment <String>]
     [-Force] [<CommonParameters>]

PARAMETERS
    -Context <String>
        Specifies the DbContext to use. If omitted, the default DbContext is used.
        指定要使用的 DbContext。如果省略，默认 DbContext 将被使用。

    -Project <String>
        Specifies the project to use. If omitted, the default project is used.
        指定要使用的项目。如果省略，默认项目将被使用。

    -StartupProject <String>
        Specifies the startup project to use. If omitted, the solution's startup project is used.
        指定要使用的启动项目。如果省略，解决方案中的启动项目将被使用。

    -Environment <String>
        Specifies the environment to use. If omitted, "Development" is used.
        指定要使用的环境。如果省略，将使用“Development”。

    -Force [<SwitchParameter>]
        Removes the last migration without checking the database. If the last migration has been applied to the database, you will need to manually reverse the changes it made.
        在移除最新的迁移时不校验数据库。如果最新的迁移已被应用到数据库，你将需要手工逆转这些改变。
````

### Scaffold-DbContext

Scaffolds a DbContext and entity type classes for a specified database.
>为指定数据库逆向生成 DbContext 和实体类型类。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````text
SYNTAX
    Scaffold-DbContext [-Connection] <String> [-Provider] <String> [-OutputDir <String>] [-Context <String>]
      [-Schemas <String>] [-Tables <String>] [-DataAnnotations] [-Force] [-Project <String>]
      [-StartupProject <String>] [-Environment <String>] [<CommonParameters>]

PARAMETERS
    -Connection <String>
        Specifies the connection string of the database.
        指定数据库的连接字符串。

    -Provider <String>
        Specifies the provider to use. For example, Microsoft.EntityFrameworkCore.SqlServer.
        指定要使用的提供者。例如，Microsoft.EntityFrameworkCore.SqlServer。

    -OutputDir <String>
        Specifies the directory to use to output the classes. If omitted, the top-level project directory is used.
        指定生成类的输出目录。如果省略，顶层项目目录将被使用。

    -Context <String>
        Specifies the name of the generated DbContext class.
        指定生成的 DBContext 类的名称。

    -Schemas <String>
        Specifies the schemas for which to generate classes.
        指定要生成类的架构。

    -Tables <String>
        Specifies the tables for which to generate classes.
        指定要要生成类的表。

    -DataAnnotations [<SwitchParameter>]
        Use DataAnnotation attributes to configure the model where possible. If omitted, the output code will use only the fluent API.
        在可能的情况下使用 DataAnnotation 特性配置模型。如果省略，输出代码将仅使用 Fluent API。

    -Force [<SwitchParameter>]
        Force scaffolding to overwrite existing files. Otherwise, the code will only proceed if no output files would be overwritten.
        强制覆盖已存在的文件。否则，代码将仅处理没有输出文件被覆盖的。

    -Project <String>
        Specifies the project to use. If omitted, the default project is used.
        指定要使用的项目。如果省略，默认项目将被使用。

    -StartupProject <String>
        Specifies the startup project to use. If omitted, the solution's startup project is used.
        指定要使用的启动项目。如果省略，解决方案中的启动项目将被使用。

    -Environment <String>
        Specifies the environment to use. If omitted, "Development" is used.
        指定要使用的环境。如果省略，将使用“Development”。
````

### Script-Migration

Generates a SQL script from migrations.
>从迁移生成 SQL 脚本。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````text
SYNTAX
    Script-Migration -From <String> -To <String> [-Idempotent] [-Context <String>] [-Project <String>]
      [-StartupProject <String>] [-Environment <String>] [<CommonParameters>]

    Script-Migration [-From <String>] [-Idempotent] [-Context <String>] [-Project <String>]
      [-StartupProject <String>] [-Environment <String>] [<CommonParameters>]

PARAMETERS
    -From <String>
        Specifies the starting migration. If omitted, '0' (the initial database) is used.
        指定开始迁移。如果省略，'0'（初始数据库）将被使用。

    -To <String>
        Specifies the ending migration. If omitted, the last migration is used.
        指定结束迁移。如果省略，最新的迁移将被使用。

    -Idempotent [<SwitchParameter>]
        Generates an idempotent script that can be used on a database at any migration.
        生成等幂脚本，它可以被使用在任何迁移的数据库上。

    -Context <String>
        Specifies the DbContext to use. If omitted, the default DbContext is used.
        指定要使用的 DbContext。如果省略，默认 DbContext 将被使用。

    -Project <String>
        Specifies the project to use. If omitted, the default project is used.
        指定要使用的项目。如果省略，默认项目将被使用。

    -StartupProject <String>
        Specifies the startup project to use. If omitted, the solution's startup project is used.
        指定要使用的启动项目。如果省略，解决方案中的启动项目将被使用。

    -Environment <String>
        Specifies the environment to use. If omitted, "Development" is used.
        指定要使用的环境。如果省略，将使用“Development”。
````

### Update-Database

Updates the database to a specified migration.
>更新数据库到特定的迁移。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````text
SYNTAX
    Update-Database [[-Migration] <String>] [-Context <String>] [-Project <String>] [-StartupProject <String>]
      [-Environment <String>] [<CommonParameters>]

PARAMETERS
    -Migration <String>
        Specifies the target migration. If '0', all migrations will be reverted. If omitted, all pending migrations will be applied.
        指定目标迁移。如果是 '0'，所有迁移将被恢复。如果省略，所有未应用到数据库的迁移将被应用。

    -Context <String>
        Specifies the DbContext to use. If omitted, the default DbContext is used.
        指定要使用的 DbContext。如果省略，默认 DbContext 将被使用。

    -Project <String>
        Specifies the project to use. If omitted, the default project is used.
        指定要使用的项目。如果省略，默认项目将被使用。

    -StartupProject <String>
        Specifies the startup project to use. If omitted, the solution's startup project is used.
        指定要使用的启动项目。如果省略，解决方案中的启动项目将被使用。

    -Environment <String>
        Specifies the environment to use. If omitted, "Development" is used.
        指定要使用的环境。如果省略，将使用“Development”。
````

### Use-DbContext

Sets the default DbContext to use.
>设置使用的默认 DbContext。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````text
SYNTAX
    Use-DbContext [-Context] <String> [-Project <String>] [-StartupProject <String>] [-Environment <String>]
      [<CommonParameters>]

PARAMETERS
    -Context <String>
        Specifies the DbContext to use.
        指定要使用的 DbContext。

    -Project <String>
        Specifies the project to use. If omitted, the default project is used.
        指定要使用的项目。如果省略，默认项目将被使用。

    -StartupProject <String>
        Specifies the startup project to use. If omitted, the solution's startup project is used.
        指定要使用的启动项目。如果省略，解决方案中的启动项目将被使用。

    -Environment <String>
        Specifies the environment to use. If omitted, "Development" is used.
        指定要使用的环境。如果省略，将使用“Development”。
````

## Using EF Core commands and EF 6 commands side-by-side
## 同时使用 EF Core 命令和 EF 6 命令

EF Core commands do not work on EF 6 or earlier version of EF. However, EF Core re-uses some of the same command names from these earlier versions. These commands can be installed side-by-side, however, EF does not automatically know which version of the command to use. This is solved by prefixing the command with the module name. The EF 6 commands PowerShell module is named "EntityFramework", and the EF Core module is named "EntityFrameworkCore". Without the prefix, PowerShell may call the wrong version of the command.
>EF Core 命令在 EF 6 或 EF 的以前版本上是不能工作的。然而，EF Core 使用了与这些早期版本相同的命令名称。这些命令可以被同时安装，但是 EF 并不能识别要使用哪个版本。
 这可以通过在命令加入模块名称前缀来解决。EF 6 命令的 PowerShell 模块被全名为“EntityFramework”，EF Core 模块被全名为“EntityFrameworkCore”。如果没带前缀，PowerShell 可能会调用了命令的错误版本。

<!-- literal_block"language": "csharp",rShell", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````PowerShell
# 调用 EF Core 命令
PS> EntityFrameworkCore\Add-Migration

# 调用 EF 6 命令
PS> EntityFramework\Add-Migration
````

## Common Errors
## 常见错误

### Error: "No parameterless constructor was found"

Design-time tools attempt to automatically find how your application creates instances of your DbContext type. If EF cannot find a suitable way to initialize your DbContext, you may encounter this error.
>设计时工具尝试自动查找你的应用程序，并创建你的 DbContext 类型的实例。如果 EF 不能找到合适的方式初始化你的 DbContext，你可能会遇到这个错误。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
No parameterless constructor was found on 'TContext'. Either add a parameterless constructor to
'TContext' or add an implementation of 'IDbContextFactory<TContext>' in the same assembly as
'TContext'.
````

As the error message suggests, one solution is to add an implementation of `IDbContextFactory<TContext>` to the current project. See [Using IDbContextFactory<TContext>](../configuring-dbcontext.md) for an example of how to create this factory.
>错误消息提示，解决办法是添加 `IDbContextFactory<TContext>` 的实现到当前项目。关于如何创建这个工厂的示例请参考 [Using IDbContextFactory<TContext>](../configuring-dbcontext.md)。

See also [Preview 2 Known Issues](dotnet.md) for .NET Core CLI commands.
>请参阅 .NET Core CLI 命令中 [预览版 2 已知问题](dotnet.md)