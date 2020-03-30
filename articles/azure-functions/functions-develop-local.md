---
title: Desenvolver e executar funções Azure localmente
description: Aprenda a codificar e testar as funções do Azure no seu computador local antes de as executar nas Funções Azure.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74230637"
---
# <a name="code-and-test-azure-functions-locally"></a>Criar código e testar as Funções do Azure localmente

Enquanto você é capaz de desenvolver e testar funções Azure no [portal Azure,]muitos desenvolvedores preferem uma experiência de desenvolvimento local. As funções facilitam a utilização do seu editor de código favorito e ferramentas de desenvolvimento para criar e testar funções no seu computador local. As suas funções locais podem ligar-se aos serviços Azure ao vivo, e pode desincomodá-los no seu computador local utilizando o tempo de funcionamento completo das Funções.

## <a name="local-development-environments"></a>Ambientes de desenvolvimento local

A forma como desenvolve funções no seu computador local depende da sua [linguagem](supported-languages.md) e das preferências de ferramentas. Os ambientes da tabela seguinte apoiam o desenvolvimento local:

|Ambiente                              |Linguagens         |Descrição|
|-----------------------------------------|------------|---|
|[Código de estúdio visual](functions-develop-vs-code.md)| [C# (biblioteca de classes)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript,](functions-reference-node.md) [PowerShell,](functions-create-first-function-powershell.md) [Python](functions-reference-python.md) | A [extensão funções Azure para código VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) adiciona suporte de funções ao Código VS. Requer as Ferramentas Nucleares. Suporta o desenvolvimento em Linux, MacOS e Windows, quando utilizar a versão 2.x das Ferramentas Core. Para saber mais, consulte [Criar a sua primeira função usando o Código do Estúdio Visual](functions-create-first-function-vs-code.md). |
| [Pedido de comando ou terminal](functions-run-local.md) | [C# (biblioteca de classes)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript,](functions-reference-node.md) [PowerShell,](functions-reference-powershell.md) [Python](functions-reference-python.md) | [As Ferramentas Nucleares das Funções Azure] fornecem o tempo de funcionamento e os modelos de funcionamento para a criação de funções, que permitem o desenvolvimento local. A versão 2.x suporta o desenvolvimento em Linux, MacOS e Windows. Todos os ambientes dependem de Ferramentas Core para o tempo de funcionamento das Funções locais. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (biblioteca de classes)](functions-dotnet-class-library.md) | As ferramentas Azure Functions estão incluídas na carga de trabalho de **desenvolvimento azure** do [Visual Studio 2019](https://www.visualstudio.com/vs/) e versões posteriores. Permite-lhe compilar funções numa biblioteca de classes e publicar o .dll para azure. Inclui as Ferramentas Core para testes locais. Para saber mais, consulte [Desenvolver funções azure utilizando o Estúdio Visual.](functions-develop-vs.md) |
| [Maven](functions-create-first-java-maven.md) (vários) | [Java](functions-reference-java.md) | Integra-se com as Ferramentas Core para permitir o desenvolvimento das funções java. A versão 2.x suporta o desenvolvimento em Linux, MacOS e Windows. Para saber mais, consulte [Criar a sua primeira função com Java e Maven.](functions-create-first-java-maven.md) Também apoia o desenvolvimento usando [eclipse](functions-create-maven-eclipse.md) e [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Cada um destes ambientes de desenvolvimento local permite criar projetos de aplicações de funções e usar modelos de Funções predefinidas para criar novas funções. Cada um usa as Ferramentas Core para que possa testar e depurar as suas funções contra o tempo de funcionamento das funções reais na sua própria máquina, tal como faria com qualquer outra aplicação. Também pode publicar o seu projeto de aplicação de funções de qualquer um destes ambientes para o Azure.  

## <a name="next-steps"></a>Passos seguintes

+ Para saber mais sobre o desenvolvimento local de funções C# compiladas usando o Visual Studio 2019, consulte [Funções De desenvolvimento Azure usando o Visual Studio](functions-develop-vs.md).
+ Para saber mais sobre o desenvolvimento local de funções usando o Código VS num computador Mac, Linux ou Windows, consulte [funções de implantação azure a partir do Código VS](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Para saber mais sobre o desenvolvimento de funções a partir do pedido de comando ou terminal, consulte [Trabalhar com ferramentas nucleares de funções Azure](functions-run-local.md).

<!-- LINKS -->

[Ferramentas centrais de funções azure]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
