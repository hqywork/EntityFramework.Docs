---
title: .NET Core CLI | Microsoft Docs
author: rowanmiller
ms.author: rowmil

ms.date: 10/27/2016

ms.assetid: c7c6824c-72be-4058-bdac-9b5b995b2f56
ms.technology: entity-framework-core
 
uid: core/miscellaneous/cli/dotnet
---
# .NET Core CLI

> [!NOTE]
> This documentation is for EF Core. For EF6.x, see [Entity Framework 6](../../../ef6/index.md).

EF command-line tools for .NET Core Command Line Interface (CLI).
>适应于 .NET Core 命令行接口（CLI）的 EF 命令行工具。

> [!NOTE]
> Command-line tools for .NET Core CLI has known issues. See [Preview 2 Known Issues](#preview-2-known-issues) for more details.
>>适应于 .NET Core CLI 的命令行工具有一些已知问题。更多信息请参阅 [预览版 2 已知问题](#preview-2-known-issues)。

## Installation
## 安装

### Prerequisites
### 先决条件

EF command-line tools requires .NET Core CLI Preview 2 or newer. See the [.NET Core](https://www.microsoft.com/net/core) website for installation instructions.
>EF 命令行工具要求 .NET Core CLI 预览版 2 或更新。请参阅 [.NET Core](https://www.microsoft.com/net/core) 网站中的安装介绍。

### Supported Frameworks
### 支持框架

EF supports .NET Core CLI commands on these frameworks:
>EF 在以下框架中支持 .NET Core CLI：

* .NET Framework 4.5.1 and newer. ("net451", "net452", "net46", etc.)

* .NET Core App 1.0. ("netcoreapp1.0")

### Install by editing project.json
### 通过编辑 project.json 安装

EF command-line tools for .NET Core CLI are installed by manually editing `project.json`.
>适用于 .NET Core CLI 的 EF 命令行工具可以通过手动编辑 `project.json` 进行安装。

1. Add `Microsoft.EntityFrameworkCore.Tools` as a "tool" and `Microsoft.EntityFrameworkCore.Design` as a build-only dependency under "dependencies". See sample project.json below.

   添加 `Microsoft.EntityFrameworkCore.Tools` 到 "tool"，并且在 "dependencies" 下添加 `Microsoft.EntityFrameworkCore.Design` 仅作为编译时依赖。请参考下面的示例 project.json。

2. Execute `dotnet restore`. If restore does not succeed, the command-line tools may not have installed correctly.

   执行 `dotnet restore`。如果没有成功还原，那可能没有正确安装命令行工具。

The resulting project.json should include these items (in addition to your other project dependencies).
>project.json 应该这些项（除了你依赖的其它项目）。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````json
{
    "dependencies": {
        "Microsoft.EntityFrameworkCore.Design": {
            "type": "build",
            "version": "1.0.0-preview2-final"
        }
    },

    "tools": {
        "Microsoft.EntityFrameworkCore.Tools": "1.0.0-preview2-final"
    },

    "frameworks": {
        "netcoreapp1.0": { }
    }
}
````

> [!TIP]
> A build-only dependency (`"type": "build"`) means this dependency is local to the current project. For example, if Project A has a build only dependency and Project B depends on A, `dotnet restore` will not add A's build-only dependencies into Project B.
>>仅编译时依赖(`"type": "build"`) 意味着这个依赖是对当前项目局部有效。例如，如果项目 A 拥有一个仅编译时依赖，并且项目 B 依赖于 A，`dotnet restore` 时将不会添加 A 的仅编译时依赖到项目 B。

## Usage
## 用法

Commands can be run from the command line by navigating to the project directory and executing `dotnet ef [subcommand]`. To see usage, add `--help` to any command to see more information about parameters and subcommands.
>命令可以在命令行通过导航到项目目录并执行 `dotnet ef [subcommand]` 来运行。可以添加 `--help` 到任一命令来查看有关参数及子命令的用法。

### dotnet-ef

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef [options] [command]

Options:
  -h|--help                           Show help information
                                      显示帮助信息
  -p|--project <PROJECT>              The project to target (defaults to the project in the current directory). Can be a path to a project.json or a project directory.
                                      目标项目（默认是当前目录中的项目）。可以指定 project.json 的路径或项目目录路径。
  -s|--startup-project <PROJECT>      The path to the project containing Startup (defaults to the target project). Can be a path to a project.json or a project directory.
                                      包含 Startup 的项目目录（默认为目标项目）。可以指定 project.json 的路径或项目目录路径。
  -c|--configuration <CONFIGURATION>  Configuration under which to load (defaults to Debug)
                                      需加载的配置（默认是 Debug）
  -f|--framework <FRAMEWORK>          Target framework to load from the startup project (defaults to the framework most compatible with .NETCoreApp,Version=v1.0).
                                      从启动项目加载的目标框架（默认是与 .NETCoreApp,Version=v1.0 兼容的框架）
  -b|--build-base-path <OUTPUT_DIR>   Directory in which to find temporary outputs.
                                      临时输出目录。
  -o|--output <OUTPUT_DIR>            Directory in which to find outputs
                                      输出目录

Commands:
  database    Commands to manage your database
              管理你的数据库的命令
  dbcontext   Commands to manage your DbContext types
              管理你的 DbContext 类型的命令
  migrations  Commands to manage your migrations
              管理你的迁移的命令
````

### dotnet-ef-database

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef database [options] [command]

Options:
  -h|--help     Show help information
                显示帮助信息
  -v|--verbose  Enable verbose output
                启用详细信息输出

Commands:
  drop    Drop the database for specific environment
          从具体环境移除数据库
  update  Updates the database to a specified migration
          更新数据库到指定迁移
````

### dotnet-ef-database-drop

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef database drop [options]

Options:
  -e|--environment <environment>  The environment to use. If omitted, "Development" is used.
                                  将使用的环境。如果省略，将使用 "Development"。
  -c|--context <context>          The DbContext to use. If omitted, the default DbContext is used
                                  将使用的 DBContext。如果省略，默认 DbContext 被使用
  -f|--force                      Drop without confirmation
                                  移除时无需确认
  -h|--help                       Show help information
                                  显示帮助信息
  -v|--verbose                    Enable verbose output
                                  启用详细信息输出
````

### dotnet-ef-database-update

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef database update [arguments] [options]

Arguments:
  [migration]  The target migration. If '0', all migrations will be reverted. If omitted, all pending migrations will be applied
               目标迁移。如果是 '0'，所有迁移将被恢复。如果省略，所有未应用到数据库的迁移将被应用。

Options:
  -c|--context <context>          The DbContext to use. If omitted, the default DbContext is used
                                  将使用的 DBContext。如果省略，默认 DbContext 被使用
  -e|--environment <environment>  The environment to use. If omitted, "Development" is used.
                                  将使用的环境。如果省略，将使用 "Development"。
  -h|--help                       Show help information
                                  显示帮助信息
  -v|--verbose                    Enable verbose output
                                  启用详细信息输出
````

### dotnet-ef-dbcontext

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef dbcontext [options] [command]

Options:
  -h|--help     Show help information
                显示帮助信息
  -v|--verbose  Enable verbose output
                启用详细信息输出

Commands:
  list      List your DbContext types
            列出你的 DBContext 类型
  scaffold  Scaffolds a DbContext and entity type classes for a specified database
            为指定数据库逆向生成 DbContext 和实体类型类。
````

### dotnet-ef-dbcontext-list

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef dbcontext list [options]

Options:
  -e|--environment <environment>  The environment to use. If omitted, "Development" is used.
                                  将使用的环境。如果省略，将使用 "Development"。
  --json                          Use json output. JSON is wrapped by '//BEGIN' and '//END'
                                  使用 JSON 格式输出。JSON 被包含在 '//BEGIN' 和 '//END' 之间
  -h|--help                       Show help information
                                  显示帮助信息
  -v|--verbose                    Enable verbose output
                                  启用详细信息输出
````

### dotnet-ef-dbcontext-scaffold

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef dbcontext scaffold [arguments] [options]

Arguments:
  [connection]  The connection string of the database
                数据库的连接字符串
  [provider]    The provider to use. For example, Microsoft.EntityFrameworkCore.SqlServer
                将要使用的提供者。例如，Microsoft.EntityFrameworkCore.SqlServer

Options:
  -a|--data-annotations           Use DataAnnotation attributes to configure the model where possible. If omitted, the output code will use only the fluent API.
                                  在可能的情况下使用 DataAnnotation 特性配置模型。如果省略，输出代码将仅使用 Fluent API。
  -c|--context <name>             Name of the generated DbContext class.
                                  指定生成的 DBContext 类的名称。
  -f|--force                      Force scaffolding to overwrite existing files. Otherwise, the code will only proceed if no output files would be overwritten.
                                  强制覆盖已存在的文件。否则，代码将仅处理没有输出文件被覆盖的。
  -o|--output-dir <path>          Directory of the project where the classes should be output. If omitted, the top-level project directory is used.
                                  指定生成类的输出目录。如果省略，顶层项目目录将被使用。
  --schema <schema>               Selects a schema for which to generate classes.
                                  指定要生成类的架构。
  -t|--table <schema.table>       Selects a table for which to generate classes.
                                  指定要要生成类的表。
  -e|--environment <environment>  The environment to use. If omitted, "Development" is used.
                                  将使用的环境。如果省略，将使用 "Development"。
  -h|--help                       Show help information
                                  显示帮助信息
  -v|--verbose                    Enable verbose output
                                  启用详细信息输出
````

### dotnet-ef-migrations

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef migrations [options] [command]

Options:
  -h|--help     Show help information
                显示帮助信息
  -v|--verbose  Enable verbose output
                启用详细信息输出

Commands:
  add     Add a new migration
          添加一个新迁移
  list    List the migrations
          列出所有的迁移
  remove  Remove the last migration
          移除最新的迁移
  script  Generate a SQL script from migrations
          从迁移生成 SQL 脚本
````

### dotnet-ef-migrations-add

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef migrations add [arguments] [options]

Arguments:
  [name]  The name of the migration
          迁移的名称

Options:
  -o|--output-dir <path>          The directory (and sub-namespace) to use. If omitted, "Migrations" is used. Relative paths are relative the directory in which the command is executed.
                                  要使用的目录（即子命令空间）。如果省略，将使用“Migrations”。相对路径是相对于项目目录。
  -c|--context <context>          The DbContext to use. If omitted, the default DbContext is used
                                  将使用的 DBContext。如果省略，默认 DbContext 被使用
  -e|--environment <environment>  The environment to use. If omitted, "Development" is used.
                                  将使用的环境。如果省略，将使用 "Development"
  --json                          Use json output. JSON is wrapped by '//BEGIN' and '//END'
                                  使用 JSON 格式输出。JSON 被包含在 '//BEGIN' 和 '//END' 之间
  -h|--help                       Show help information
                                  显示帮助信息
  -v|--verbose                    Enable verbose output
                                  启用详细信息输出
````

### dotnet-ef-migrations-list

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef migrations list [options]

Options:
  -c|--context <context>          The DbContext to use. If omitted, the default DbContext is used
                                  将使用的 DBContext。如果省略，默认 DbContext 被使用
  -e|--environment <environment>  The environment to use. If omitted, "Development" is used.
                                  将使用的环境。如果省略，将使用 "Development"
  --json                          Use json output. JSON is wrapped by '//BEGIN' and '//END'
                                  使用 JSON 格式输出。JSON 被包含在 '//BEGIN' 和 '//END' 之间
  -h|--help                       Show help information
                                  显示帮助信息
  -v|--verbose                    Enable verbose output
                                  启用详细信息输出
````

### dotnet-ef-migrations-remove

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef migrations remove [options]

Options:
  -c|--context <context>          The DbContext to use. If omitted, the default DbContext is used
                                  将使用的 DBContext。如果省略，默认 DbContext 被使用
  -e|--environment <environment>  The environment to use. If omitted, "Development" is used.
                                  将使用的环境。如果省略，将使用 "Development"。
  -f|--force                      Removes the last migration without checking the database. If the last migration has been applied to the database, you will need to manually reverse the changes it made.
                                  在移除最新的迁移时不校验数据库。如果最新的迁移已被应用到数据库，你将需要手工逆转这些改变。
  -h|--help                       Show help information
                                  显示帮助信息
  -v|--verbose                    Enable verbose output
                                  启用详细信息输出
````

### dotnet-ef-migrations-script

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Usage: dotnet ef migrations script [arguments] [options]

Arguments:
  [from]  The starting migration. If omitted, '0' (the initial database) is used
          指定开始迁移。如果省略，'0'（初始数据库）将被使用
  [to]    The ending migration. If omitted, the last migration is used
          指定结束迁移。如果省略，最新的迁移将被使用。

Options:
  -o|--output <file>              The file to write the script to instead of stdout
                                  写入脚本的文件，用来替代标准输出
  -i|--idempotent                 Generates an idempotent script that can used on a database at any migration
                                  生成等幂脚本，它可以被使用在任何迁移的数据库上。
  -c|--context <context>          The DbContext to use. If omitted, the default DbContext is used
                                  将使用的 DBContext。如果省略，默认 DbContext 被使用
  -e|--environment <environment>  The environment to use. If omitted, "Development" is used.
                                  将使用的环境。如果省略，将使用 "Development"。
  -h|--help                       Show help information
                                  显示帮助信息
  -v|--verbose                    Enable verbose output
                                  启用详细信息输出
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
>>错误消息提示，解决办法是添加 `IDbContextFactory<TContext>` 的实现到当前项目。关于如何创建这个工厂的示例请参考 [Using IDbContextFactory<TContext>](../configuring-dbcontext.md)。

<a name=dotnet-cli-issues></a>

## Preview 2 Known Issues
## 预览版 2 已知问题

### Targeting class library projects is not supported
### 目标类库项目是不被支持的

.NET Core CLI does not support running commands on class libraries as of Preview 2. Despite being able to install EF tools, executing commands may throw this error message.
>在 预览版 2 中，.NET Core CLI 不支持在类库上运行命令。尽管可以安装 EF 工具，但执行命令时可能会抛出这些错误消息。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````
Could not invoke this command on the startup project '(your project name)'. This preview of Entity Framework tools does not support commands on class library projects in ASP.NET Core and .NET Core applications.
````

See issue [https://github.com/dotnet/cli/issues/2645](https://github.com/dotnet/cli/issues/2645).
>请参阅问题 [https://github.com/dotnet/cli/issues/2645](https://github.com/dotnet/cli/issues/2645)。

#### Explanation
#### 说明

If `dotnet run` does not work in the startup project, then `dotnet ef` cannot run either.
>如果 `dotnet run`  在启动项目上不能工作，那么 `dotnet ef` 同样不能运行。

"dotnet ef" is invoked as an alternate entry point into an application. If the "startup" project is not an application, then it is not currently possible to run the project as an application with the "dotnet ef" alternate entry point.
>"dotnet ef" 将被作为应用程序的入口点替身运行。如果 "startup" 不是一个应用程序，那么它不可能作为应用程序的替身入口点 "dotnet ef" 来运行项目。

The "startup" project defaults to the current project, unless specified differently with the parameter `--startup-project`.
>"startup" 项目黑夜是当前项目，除非使用参数  `--startup-project` 进行了指定。

#### Workaround 1 - Utilize a separate startup project
#### 方法 1 - 利用一个单独的启动项目

Convert the class library project into an "app" project. This can either be a .NET Core app or a desktop .NET app.
>将类库项目转换为 "app" 项目。这可以是一个 .NET Core 应用或桌面 .NET 应用。

Example:
>示例：

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````json
{
    "frameworks": {
        "netcoreapp1.0": {
            "dependencies": {
                "Microsoft.NETCore.App": {
                    "type": "platform",
                    "version": "1.0.0-*"
                }
            }
        }
    }
}
````

Be sure to register the EntityFramework Tools as a project dependency and in the tools section of your project.json.
>务必在你的 project.json 文件中的依赖节和工具节注册 EntityFramework 工具。

Example:
>示例：

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````json
{
    "dependencies": {
        "Microsoft.EntityFrameworkCore.Tools": {
            "version": "1.0.0-preview2-final",
            "type": "build"
        }
    },
    "tools": {
        "Microsoft.EntityFrameworkCore.Tools": "1.0.0-preview2-final"
    }
}
````

Finally, specify a startup project that is a "runnable app."
>最后，指定启动项目是“可运行的应用”。

Example:
>示例：

<!-- literal_block"language": "csharp",ole", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````console
dotnet ef --startup-project ../MyConsoleApplication/ migrations list
````

#### Workaround 2 - Modify your class library to be a startup application
#### 方法 2 - 修改你的类库，变成一个启动应用程序

Convert the class library project into an "app" project. This can either be a .NET Core app or a desktop .NET app.
>将类库项目转换为 "app" 项目。这可以是一个 .NET Core 应用或桌面 .NET 应用。

To make the project a .NET Core App, add the "netcoreapp1.0" framework to project.json along with the other settings in the sample below:
>要创建 .NET Core 应用项目，添加 "netcoreapp1.0" 框架到 project.json，连同下面示例中的其它设置。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````json
{
    "buildOptions": {
        "emitEntryPoint": true
    },
    "frameworks": {
        "netcoreapp1.0": {
            "dependencies": {
                "Microsoft.NETCore.App": {
                    "type": "platform",
                    "version": "1.0.0-*"
                }
            }
        }
    }
}
````

To make a desktop .NET app, ensure you project targets "net451" or newer (example "net461" also works) and ensure the build option `"emitEntryPoint"` is set to true.
>要创建桌面 .NET 应用，确保你的项目目标是 "net451" 或以上（例如在 "net461" 同样可以工作），并且确保编译选项 `"emitEntryPoint"` 被设置为真。

<!-- literal_block"language": "csharp",", "xml:space": "preserve", "classes  "backrefs  "names  "dupnames  highlight_args}, "ids  "linenos": false -->
````json
{
    "buildOptions": {
        "emitEntryPoint": true
    },
    "frameworks": {
        "net451": { }
    }
}
````
