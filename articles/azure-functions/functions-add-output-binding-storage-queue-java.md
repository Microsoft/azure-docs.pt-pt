---
title: Ligue a sua função Java ao Armazenamento Azure
description: Aprenda a ligar uma função Java acionada por HTTP ao Azure Storage utilizando uma ligação de saída de armazenamento de fila.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: b4381c4acbea8a3b7d86d9c32aaf9cea24cf15fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422659"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Ligue a sua função Java ao Armazenamento Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra-lhe como integrar a função que criou no [artigo de arranque anterior](./create-first-function-cli-java.md?tabs=bash,browser) com uma fila de Armazenamento Azure. A vinculação de saída que adiciona a esta função escreve dados de um pedido HTTP para uma mensagem na fila.

A maioria das ligações requer uma cadeia de ligação armazenada que as funções usam para aceder ao serviço vinculado. Para facilitar esta ligação, utilize a conta de Armazenamento que criou com a sua aplicação de função. A ligação a esta conta já está armazenada numa definição de aplicação chamada `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, complete os passos na [parte 1 do arranque rápido de Java.](./create-first-function-cli-java.md?tabs=bash,browser)

## <a name="download-the-function-app-settings"></a>Descarregue as definições da aplicação de funções

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Ativar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Pode agora adicionar a ligação de saída de Armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Está agora pronto para experimentar a nova ligação de saída localmente.

## <a name="run-the-function-locally"></a>Executar a função localmente

Como antes, utilize o seguinte comando para construir o projeto e iniciar o tempo de execução das Funções localmente:

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
> Como ativou pacotes de extensões no host.jsligado, a [extensão de ligação de Armazenamento](functions-bindings-storage-blob.md#add-to-your-functions-app) foi descarregada e instalada para si durante o arranque, juntamente com as outras extensões de ligação da Microsoft.

Como antes, desencadeie a função da linha de comando utilizando o CURL numa nova janela do terminal:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Desta vez, a ligação de saída também cria uma fila nomeada `outqueue` na sua conta de Armazenamento e adiciona uma mensagem com esta mesma cadeia.

Em seguida, use o CLI Azure para ver a nova fila e verificar se uma mensagem foi adicionada. Também pode ver a sua fila utilizando o [Microsoft Azure Storage Explorer][Azure Storage Explorer] ou no portal [Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Recolocar o projeto 

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

Mais uma vez, pode utilizar o cURL para testar a função implantada. Como antes, passe o valor `AzureFunctions` no corpo do pedido DOM para a URL, como neste exemplo:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Pode examinar novamente [a mensagem da fila de armazenamento](#query-the-storage-queue) para verificar se a ligação de saída gera uma nova mensagem na fila, como esperado.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função de desencadeamento HTTP para escrever dados numa fila de armazenamento. Para saber mais sobre o desenvolvimento de Funções Azure com Java, consulte o [guia de desenvolvimento de Java funções Azure e](functions-reference-java.md) as [funções Azure.](functions-triggers-bindings.md) Por exemplo, de projetos completos de funções em Java, consulte as [amostras de Funções de Java.](/samples/browse/?products=azure-functions&languages=Java) 

Em seguida, deve ativar a monitorização do Application Insights para a sua aplicação de função:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/
