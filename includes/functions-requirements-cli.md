---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 951dbb6c1a44e3c081f682db49458be165e9e930
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553986"
---
## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ A versão 2.7.1846 ou uma versão posterior 2.x do [Azure Functions.](../articles/azure-functions/functions-run-local.md#v2)
::: zone-end  
::: zone pivot="programming-language-python"
+ A versão Azure Functions Core Tools corresponde à versão Python instalada:

   | Versão de Python | Versão Core Tools |
   | -------------- | ------------------ |
   | Python 3.8     | [versão 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Versão 2.7.1846 ou versão posterior](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ A versão 2.4 ou mais tarde do [Azure CLI.](/cli/azure/install-azure-cli) 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Versões LTS ativas e de manutenção (8.11.1 e 10.14.1 recomendado).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bit)](https://www.python.org/downloads/release/python-368/), que são suportados por Funções Azure. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ O [.NET Core SDK 3.1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ O [Kit de Desenvolvimento java,](/azure/developer/java/fundamentals/java-jdk-long-term-support)versão 8 ou 11. 

+ [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior.

::: zone-end