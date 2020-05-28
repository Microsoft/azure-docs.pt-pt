---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: dfacbfbc57ec536eb983394c28920da719af4b1d
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996533"
---
## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ O [Azure Funs Core Tools](../articles/azure-functions/functions-run-local.md#v2) versão 2.7.1846 ou uma versão posterior de 2.x.
::: zone-end  
::: zone pivot="programming-language-python"
+ A versão Azure Functions Core Tools corresponde à sua versão Python instalada:

   | Versão de Python | Versão Core Tools |
   | -------------- | ------------------ |
   | Python 3.8     | [versão 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Versão 2.7.1846 ou uma versão 2.x posterior](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ A versão [Azure CLI](/cli/azure/install-azure-cli) 2.4 ou posterior. 
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
