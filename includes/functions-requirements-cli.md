---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: e969520e226999bc2b104fb4f757619832eaa165
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88944470"
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
+ O [Kit de Desenvolvimento java,](https://aka.ms/azure-jdks)versão 8. 

    > [!IMPORTANT]
    > + O suporte de funções para Java 11 está atualmente em pré-visualização, e o arquétipo Maven cria uma implementação Java 8 por padrão. Se pretender executar a sua aplicação de função em Java 11, tem de atualizar manualmente o ficheiro pom.xml com os valores de Java 11. Para saber mais, consulte as [versões Java.](../articles/azure-functions/functions-reference-java.md#java-versions) 
    > + A `JAVA_HOME` variável ambiente deve ser definida para a localização de instalação da versão correta do JDK para completar este arranque rápido.

+ [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior.

::: zone-end
