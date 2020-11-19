---
title: Desenvolver e executar Funções Azure localmente
description: Saiba como codificar e testar funções Azure no seu computador local antes de executá-las em Funções Azure.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: c011dff9f43de59d3f08445a7bbd5387b27563ad
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920037"
---
# <a name="code-and-test-azure-functions-locally"></a>Criar código e testar as Funções do Azure localmente

Enquanto você é capaz de desenvolver e testar Funções Azure no [portal Azure], muitos desenvolvedores preferem uma experiência de desenvolvimento local. As funções facilitam a utilização do seu editor de código favorito e ferramentas de desenvolvimento para criar e testar funções no seu computador local. As suas funções locais podem ligar-se aos serviços Azure vivos, e pode desorgá-las no seu computador local utilizando o tempo de funcionamento completo das Funções.

## <a name="local-development-environments"></a>Ambientes de desenvolvimento local

A forma como desenvolve funções no computador local depende da sua [língua](supported-languages.md) e preferências de ferramentas. Os ambientes da tabela seguinte apoiam o desenvolvimento local:

|Ambiente                              |Idiomas         |Descrição|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C. (biblioteca de classes)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript,](functions-reference-node.md) [PowerShell,](./create-first-function-vs-code-powershell.md) [Python](functions-reference-python.md) | A [extensão de Funções Azure para código VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) adiciona suporte de funções ao Código VS. Requer as Ferramentas Centrais. Suporta o desenvolvimento em Linux, MacOS e Windows, ao utilizar a versão 2.x das Ferramentas Core. Para saber mais, consulte [Criar a sua primeira função utilizando o Código do Estúdio Visual.](functions-create-first-function-vs-code.md) |
| [Pedido de comando ou terminal](functions-run-local.md) | [C. (biblioteca de classes)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript,](functions-reference-node.md) [PowerShell,](functions-reference-powershell.md) [Python](functions-reference-python.md) | [As Ferramentas Principais das Funções Azure] fornecem o tempo de execução do núcleo e os modelos para a criação de funções, que permitem o desenvolvimento local. A versão 2.x suporta o desenvolvimento em Linux, MacOS e Windows. Todos os ambientes dependem de Ferramentas Centrais para o tempo de funcionamento das funções locais. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (biblioteca de classes)](functions-dotnet-class-library.md) | As ferramentas Azure Functions estão incluídas na carga de trabalho de desenvolvimento do **Azure** do [Visual Studio 2019](https://www.visualstudio.com/vs/) e versões posteriores. Permite-lhe compilar funções numa biblioteca de classes e publicar o .dll ao Azure. Inclui as Ferramentas Centrais para testes locais. Para saber mais, consulte [Desenvolver Funções Azure utilizando o Visual Studio](functions-develop-vs.md). |
| [Maven](./create-first-function-cli-java.md) (vários) | [Java](functions-reference-java.md) | Integra-se com Ferramentas Core para permitir o desenvolvimento de funções java. A versão 2.x suporta o desenvolvimento em Linux, MacOS e Windows. Para saber mais, consulte [Criar a sua primeira função com Java e Maven.](./create-first-function-cli-java.md) Também apoia o desenvolvimento usando [Eclipse](functions-create-maven-eclipse.md) e [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Cada um destes ambientes de desenvolvimento local permite criar projetos de aplicações de função e usar modelos de Funções predefinidas para criar novas funções. Cada um usa as Ferramentas Core para que possa testar e desordá-lo contra o tempo de funcionamento das funções reais na sua própria máquina, tal como qualquer outra aplicação. Também pode publicar o seu projeto de aplicação de função de qualquer um destes ambientes para o Azure.

## <a name="next-steps"></a>Passos seguintes

+ Para saber mais sobre o desenvolvimento local das funções C# compiladas utilizando o Visual Studio 2019, consulte [Develop Azure Functions using Visual Studio](functions-develop-vs.md).
+ Para saber mais sobre o desenvolvimento local de funções utilizando o Código VS num computador Mac, Linux ou Windows, consulte [Funções de Azure de Implementação a partir do Código VS](/azure/developer/javascript/tutorial-vscode-serverless-node-01).
+ Para saber mais sobre o desenvolvimento de funções a partir do comando ou terminal, consulte [Trabalhar com As Ferramentas Principais de Funções Azure](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal do Azure]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
