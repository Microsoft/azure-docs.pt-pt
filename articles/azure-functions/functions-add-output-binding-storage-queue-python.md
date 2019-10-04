---
title: Adicionar uma associação de fila de armazenamento do Azure à sua função Python
description: Saiba como adicionar uma associação de saída de fila do armazenamento do Azure à sua função do Python usando as ferramentas básicas do CLI do Azure e do functions.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839184"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Adicionar uma associação de fila de armazenamento do Azure à sua função Python

Azure Functions permite que você conecte os serviços do Azure e outros recursos a funções sem precisar escrever seu próprio código de integração. Essas *associações*, que representam entrada e saída, são declaradas dentro da definição da função. Os dados das associações são fornecidos para a função como parâmetros. Um *gatilho* é um tipo especial de associação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e saída. Para saber mais, confira [Azure Functions os conceitos de gatilhos e associações](functions-triggers-bindings.md).

Este artigo mostra como integrar a função que você criou no artigo de [início rápido anterior](functions-create-first-function-python.md) com uma fila de armazenamento do Azure. A associação de saída que você adiciona a essa função grava dados de uma solicitação HTTP em uma mensagem na fila.

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço associado. Para facilitar essa conexão, use a conta de armazenamento que você criou com seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de aplicativo chamada `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, conclua as etapas na [parte 1 do início rápido do Python](functions-create-first-function-python.md).

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora você pode adicionar a associação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em funções, cada tipo de associação requer um `direction`, `type` e um @no__t exclusivo a ser definido no arquivo function. JSON. A maneira como você define esses atributos depende do idioma do seu aplicativo de funções.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

Ao usar uma associação de saída, você não precisa usar o código do SDK de armazenamento do Azure para autenticação, obter uma referência de fila ou gravar dados. O tempo de execução de funções e a associação de saída de fila executam essas tarefas para você.

## <a name="run-the-function-locally"></a>Executar localmente a função

Como antes, use o seguinte comando para iniciar o tempo de execução do Functions localmente:

```bash
func host start
```

> [!NOTE]  
> Como no início rápido anterior você habilitou pacotes de extensão no host. JSON, a [extensão de associação de armazenamento](functions-bindings-storage-blob.md#packages---functions-2x) foi baixada e instalada para você durante a inicialização, juntamente com as outras extensões de associação da Microsoft.

Copie o URL da função `HttpTrigger` do resultado do runtime e cole-o na barra de endereço do browser. Acrescente a cadeia de caracteres de consulta `?name=<yourname>` a essa URL e execute a solicitação. Você deve ver a mesma resposta no navegador como fez no artigo anterior.

Desta vez, a associação de saída também cria uma fila chamada `outqueue` em sua conta de armazenamento e adiciona uma mensagem com essa mesma cadeia de caracteres.

Em seguida, use o CLI do Azure para exibir a nova fila e verificar se uma mensagem foi adicionada. Você também pode exibir sua fila usando o [Gerenciador de armazenamento do Microsoft Azure][Azure Storage Explorer] ou no [portal do Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Definir a conexão da conta de armazenamento

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Consultar a fila de armazenamento

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

Agora é hora de republicar o aplicativo de funções atualizado no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Novamente, você pode usar a ondulação ou um navegador para testar a função implantada. Como antes, acrescente a cadeia de caracteres de consulta `&name=<yourname>` à URL, como neste exemplo:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Você pode [examinar a mensagem da fila de armazenamento](#query-the-storage-queue) para verificar se a associação de saída gera novamente uma nova mensagem na fila.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Você atualizou sua função disparada por HTTP para gravar dados em uma fila de armazenamento. Para saber mais sobre como desenvolver Azure Functions com Python, confira o [Guia do desenvolvedor Azure Functions Python](functions-reference-python.md) e [Azure Functions gatilhos e associações](functions-triggers-bindings.md). Para obter exemplos de projetos de funções completos no Python, consulte os [exemplos de funções do Python](/samples/browse/?products=azure-functions&languages=python). 

Em seguida, você deve habilitar o monitoramento de Application Insights para seu aplicativo de funções:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/