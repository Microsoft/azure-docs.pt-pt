---
title: Desenvolver e executar o Azure Functions localmente
description: Saiba como codificar e testar o Azure Functions em seu computador local antes de executá-los em Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230637"
---
# <a name="code-and-test-azure-functions-locally"></a>Codificar e testar Azure Functions localmente

Embora você possa desenvolver e testar Azure Functions na [portal do Azure], muitos desenvolvedores preferem uma experiência de desenvolvimento local. O Functions facilita o uso do editor de código favorito e das ferramentas de desenvolvimento para criar e testar funções no computador local. Suas funções locais podem se conectar aos serviços do Azure ao vivo e você pode depurá-los em seu computador local usando o tempo de execução do Functions completo.

## <a name="local-development-environments"></a>Ambientes de desenvolvimento local

A maneira pela qual você desenvolve funções em seu computador local depende de suas preferências de [idioma](supported-languages.md) e de ferramentas. Os ambientes na tabela a seguir dão suporte ao desenvolvimento local:

|Ambiente                              |Linguagens         |Descrição|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [](functions-create-first-function-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(biblioteca de classes), script (. CSX), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | A [extensão de Azure Functions para vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) adiciona suporte a funções para vs Code. Requer as ferramentas principais. Dá suporte ao desenvolvimento em Linux, MacOS e Windows, ao usar a versão 2. x das principais ferramentas. Para saber mais, confira [criar sua primeira função usando Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Prompt de comando ou terminal](functions-run-local.md) | [](functions-reference-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(biblioteca de classes), script (. CSX), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | [Azure Functions Core Tools] fornece o tempo de execução principal e os modelos para a criação de funções, que habilitam o desenvolvimento local. A versão 2. x dá suporte ao desenvolvimento em Linux, MacOS e Windows. Todos os ambientes dependem de ferramentas principais para o tempo de execução de funções locais. |
| [Visual Studio 2019](functions-develop-vs.md) | [C#(biblioteca de classes)](functions-dotnet-class-library.md) | As ferramentas de Azure Functions estão incluídas na carga de trabalho de **desenvolvimento do Azure** do [Visual Studio 2019](https://www.visualstudio.com/vs/) e versões posteriores. Permite compilar funções em uma biblioteca de classes e publicar o. dll no Azure. Inclui as principais ferramentas para teste local. Para saber mais, consulte [desenvolver Azure Functions usando o Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (vários) | [Java](functions-reference-java.md) | Integra-se com as ferramentas básicas para habilitar o desenvolvimento de funções Java. A versão 2. x dá suporte ao desenvolvimento em Linux, MacOS e Windows. Para saber mais, confira [criar sua primeira função com Java e Maven](functions-create-first-java-maven.md). Também dá suporte ao desenvolvimento usando o [Eclipse](functions-create-maven-eclipse.md) e o [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Cada um desses ambientes de desenvolvimento local permite criar projetos de aplicativo de função e usar modelos de funções predefinidas para criar novas funções. Cada uma usa as ferramentas principais para que você possa testar e depurar suas funções em relação ao tempo de execução real Functions em sua própria máquina, assim como faria com qualquer outro aplicativo. Você também pode publicar seu projeto de aplicativo de funções de qualquer um desses ambientes para o Azure.  

## <a name="next-steps"></a>Passos seguintes

+ Para saber mais sobre o desenvolvimento local de C# funções compiladas usando o visual Studio 2019, consulte [desenvolver Azure Functions usando o Visual Studio](functions-develop-vs.md).
+ Para saber mais sobre o desenvolvimento local de funções usando o VS Code em um computador Mac, Linux ou Windows, consulte [implantar Azure Functions de vs Code](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Para saber mais sobre como desenvolver funções no prompt de comando ou terminal, confira [trabalhar com Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal do Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
