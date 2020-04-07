---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673114"
---
## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ O [Azure Funs Core Tools](../articles/azure-functions/functions-run-local.md#v2) versão 2.7.1846 ou uma versão posterior de 2.x.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 e 3.7 requerem [funções azure Core Tools](../articles/azure-functions/functions-run-local.md#v2) versão 2.7.1846 ou uma versão 2.x posterior. Python 3.8 requer [versão 3.x](../articles/azure-functions/functions-run-local.md#v2) das Ferramentas Core.
::: zone-end

+ A versão [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 ou posterior. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js,](https://nodejs.org/)itldes ative LTS e manutenção LTS (8.11.1 e 10.14.1 recomendados).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-bit),](https://www.python.org/downloads/release/python-382/) [Python 3.7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bit)](https://www.python.org/downloads/release/python-368/), que são apoiados por Funções Azure. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ O [Núcleo .NET SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ O [Kit de Desenvolvimento Java,](https://aka.ms/azure-jdks)versão 8.

+ [Apache Maven](https://maven.apache.org), versão 3.0 ou superior.

> [!IMPORTANT]
> A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.
::: zone-end