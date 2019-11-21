---
title: Develop and run Azure functions locally
description: Learn how to code and test Azure functions on your local computer before you run them on Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230637"
---
# <a name="code-and-test-azure-functions-locally"></a>Code and test Azure Functions locally

While you're able to develop and test Azure Functions in the [Portal do Azure], many developers prefer a local development experience. Functions makes it easy to use your favorite code editor and development tools to create and test functions on your local computer. Your local functions can connect to live Azure services, and you can debug them on your local computer using the full Functions runtime.

## <a name="local-development-environments"></a>Local development environments

The way in which you develop functions on your local computer depends on your [language](supported-languages.md) and tooling preferences. The environments in the following table support local development:

|Ambiente                              |Idiomas         |Descrição|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (class library)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | The [Azure Functions extension for VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) adds Functions support to VS Code. Requires the Core Tools. Supports development on Linux, MacOS, and Windows, when using version 2.x of the Core Tools. To learn more, see [Create your first function using Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Command prompt or terminal](functions-run-local.md) | [C# (class library)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Azure Functions Core Tools] provides the core runtime and templates for creating functions, which enable local development. Version 2.x supports development on Linux, MacOS, and Windows. All environments rely on Core Tools for the local Functions runtime. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (class library)](functions-dotnet-class-library.md) | The Azure Functions tools are included in the **Azure development** workload of [Visual Studio 2019](https://www.visualstudio.com/vs/) and later versions. Lets you compile functions in a class library and publish the .dll to Azure. Includes the Core Tools for local testing. To learn more, see [Develop Azure Functions using Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (various) | [Java](functions-reference-java.md) | Integrates with Core Tools to enable development of Java functions. Version 2.x supports development on Linux, MacOS, and Windows. To learn more, see [Create your first function with Java and Maven](functions-create-first-java-maven.md). Also supports development using [Eclipse](functions-create-maven-eclipse.md) and [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Each of these local development environments lets you create function app projects and use predefined Functions templates to create new functions. Each uses the Core Tools so that you can test and debug your functions against the real Functions runtime on your own machine just as you would any other app. You can also publish your function app project from any of these environments to Azure.  

## <a name="next-steps"></a>Passos seguintes

+ To learn more about local development of compiled C# functions using Visual Studio 2019, see [Develop Azure Functions using Visual Studio](functions-develop-vs.md).
+ To learn more about local development of functions using VS Code on a Mac, Linux, or Windows computer, see [Deploy Azure Functions from VS Code](/azure/javascript/tutorial-vscode-serverless-node-01).
+ To learn more about developing functions from the command prompt or terminal, see [Work with Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal do Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
