---
title: Ligue a sua função Java ao Armazenamento Azure
description: Aprenda a ligar uma função Java acionada pelo HTTP ao Armazenamento Azure utilizando uma ligação de saída de armazenamento de fila.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d9815fd27a57acc8b418962e610d2ae1c106edde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673263"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Ligue a sua função Java ao Armazenamento Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra-lhe como integrar a função que criou no [artigo quickstart anterior](functions-create-first-java-maven.md) com uma fila de Armazenamento Azure. O encadernação de saída que adiciona a esta função escreve dados de um pedido HTTP para uma mensagem na fila.

A maioria das ligações requer uma cadeia de ligação armazenada que as Funções usam para aceder ao serviço de encadernação. Para facilitar esta ligação, utiliza a conta de Armazenamento que criou com a sua aplicação de funções. A ligação a esta conta já está `AzureWebJobsStorage`armazenada numa definição de aplicação chamada .  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, complete os passos na [parte 1 do início rápido de Java.](functions-create-first-java-maven.md)

## <a name="download-the-function-app-settings"></a>Descarregue as definições da aplicação de funções

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Ativar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora pode adicionar a ligação de saída de Armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Está pronto para experimentar a nova ligação de saída local.

## <a name="run-the-function-locally"></a>Executar localmente a função

Como antes, utilize o seguinte comando para construir o projeto e iniciar o funcionamento das Funções localmente:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Uma vez que permitiu pacotes de extensão no host.json, a extensão de [encadernação de Armazenamento](functions-bindings-storage-blob.md#add-to-your-functions-app) foi descarregada e instalada para si durante o arranque, juntamente com as outras extensões de ligação da Microsoft.

Como antes, acionar a função a partir da linha de comando utilizando cURL numa nova janela de terminais:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Desta vez, a ligação de `outqueue` saída também cria uma fila nomeada na sua conta de Armazenamento e adiciona uma mensagem com esta mesma corda.

Em seguida, usa o Azure CLI para ver a nova fila e verificar se foi adicionada uma mensagem. Também pode ver a sua fila utilizando o [Microsoft Azure Storage Explorer][Azure Storage Explorer] ou no portal [Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Reutilizar o projeto 

Para atualizar a sua aplicação publicada, volte a executar o seguinte comando:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Mais uma vez, pode utilizar cURL para testar a função implantada. Como antes, passe `AzureFunctions` o valor no corpo do pedido post para o URL, como neste exemplo:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Pode [examinar novamente a mensagem de fila](#query-the-storage-queue) de armazenamento para verificar se a ligação de saída gera uma nova mensagem na fila, como esperado.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função de HTTP para escrever dados para uma fila de Armazenamento. Para saber mais sobre o desenvolvimento de Funções Azure com Java, consulte o guia de desenvolvimento das [Funções Azure Java](functions-reference-java.md) e os [gatilhos e encadernações de Funções Azure.](functions-triggers-bindings.md) Por exemplo, em Java, consulte as amostras de [Funções Java.](/samples/browse/?products=azure-functions&languages=Java) 

Em seguida, deve ativar a monitorização de Insights de Aplicação para a sua aplicação de funções:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
