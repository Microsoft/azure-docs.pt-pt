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
ms.openlocfilehash: 9fdbf3466256c5e24de17541770fa2095fcf38a4
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171089"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Adicionar uma associação de fila de armazenamento do Azure à sua função Python

Azure Functions permite que você conecte os serviços do Azure e outros recursos a funções sem precisar escrever seu próprio código de integração. Essas *associações*, que representam entrada e saída, são declaradas dentro da definição da função. Os dados das associações são fornecidos para a função como parâmetros. Um *gatilho* é um tipo especial de associação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e saída. Para saber mais, confira [Azure Functions os conceitos de gatilhos e associações](functions-triggers-bindings.md).

Este artigo mostra como integrar a função que você criou no artigo de [início rápido anterior](functions-create-first-function-python.md) com uma fila de armazenamento do Azure. A associação de saída que você adiciona a essa função grava dados de uma solicitação HTTP em uma mensagem na fila.

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço associado. Para facilitar essa conexão, use a conta de armazenamento que você criou com seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de `AzureWebJobsStorage`aplicativo chamada.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, conclua as etapas na [parte 1 do início rápido do Python](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

No artigo de início rápido anterior, você criou um aplicativo de funções no Azure, junto com a conta de armazenamento necessária. A cadeia de conexão para essa conta é armazenada com segurança nas configurações do aplicativo no Azure. Neste artigo, você grava mensagens em uma fila de armazenamento na mesma conta. Para se conectar à sua conta de armazenamento ao executar a função localmente, você deve baixar as configurações do aplicativo para o arquivo local. Settings. JSON. Execute o seguinte comando Azure Functions Core Tools para baixar as configurações para local. Settings. JSON, `<APP_NAME>` substituindo pelo nome do seu aplicativo de funções do artigo anterior:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Talvez seja necessário entrar em sua conta do Azure.

> [!IMPORTANT]  
> Como ele contém segredos, o arquivo local. Settings. JSON nunca é publicado e deve ser excluído do controle do código-fonte.

Você precisa do valor `AzureWebJobsStorage`, que é a cadeia de conexão da conta de armazenamento. Use essa conexão para verificar se a associação de saída funciona conforme o esperado.

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora você pode adicionar a associação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em funções, cada tipo de associação requer que um `direction`, a `type`e um exclusivo `name` sejam definidos no arquivo function. JSON. Dependendo do tipo de associação, podem ser necessárias propriedades adicionais. A [configuração de saída de fila](functions-bindings-storage-queue.md#output---configuration) descreve os campos necessários para uma associação de fila de armazenamento do Azure.

Para criar uma associação, você adiciona um objeto de configuração de associação ao arquivo function. JSON. Edite o arquivo function. JSON em sua pasta HttpTrigger para adicionar um objeto à `bindings` matriz que tem estas propriedades:

| Propriedade | Value | Descrição |
| -------- | ----- | ----------- |
| **`name`** | `msg` | O nome que identifica o parâmetro de associação referenciado no seu código. |
| **`type`** | `queue` | A associação é uma associação de fila de armazenamento do Azure. |
| **`direction`** | `out` | A associação é uma associação de saída. |
| **`queueName`** | `outqueue` | O nome da fila na qual a associação é gravada. Quando o `queueName` não existe, a associação o cria no primeiro uso. |
| **`connection`** | `AzureWebJobsStorage` | O nome de uma configuração de aplicativo que contém a cadeia de conexão para a conta de armazenamento. A `AzureWebJobsStorage` configuração contém a cadeia de conexão para a conta de armazenamento que você criou com o aplicativo de funções. |

Seu arquivo function. JSON agora deve se parecer com este exemplo:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Depois que `name` o é configurado, você pode começar a usá-lo para acessar a associação como um atributo de método na assinatura de função. No exemplo a seguir, `msg` é uma instância [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)do.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

Ao usar uma associação de saída, você não precisa usar o código do SDK de armazenamento do Azure para autenticação, obter uma referência de fila ou gravar dados. O tempo de execução de funções e a associação de saída de fila executam essas tarefas para você.

## <a name="run-the-function-locally"></a>Executar localmente a função

Como antes, use o seguinte comando para iniciar o tempo de execução do Functions localmente:

```bash
func host start
```

> [!NOTE]  
> Como no início rápido anterior você habilitou pacotes de extensão no host. JSON, a [extensão de associação de armazenamento](functions-bindings-storage-blob.md#packages---functions-2x) foi baixada e instalada para você durante a inicialização, juntamente com as outras extensões de associação da Microsoft.

Copie o URL da função `HttpTrigger` do resultado do runtime e cole-o na barra de endereço do browser. Acrescente a cadeia de `?name=<yourname>` caracteres de consulta a essa URL e execute a solicitação. Você deve ver a mesma resposta no navegador como fez no artigo anterior.

Desta vez, a associação de saída também cria uma fila `outqueue` chamada em sua conta de armazenamento e adiciona uma mensagem com essa mesma cadeia de caracteres.

Em seguida, use o CLI do Azure para exibir a nova fila e verificar se uma mensagem foi adicionada. Você também pode exibir sua fila usando o [Gerenciador de armazenamento do Microsoft Azure][Azure Storage Explorer] ou no [portal do Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Definir a conexão da conta de armazenamento

Abra o arquivo local. Settings. JSON e copie o valor de `AzureWebJobsStorage`, que é a cadeia de conexão da conta de armazenamento. Defina a `AZURE_STORAGE_CONNECTION_STRING` variável de ambiente para a cadeia de conexão usando este comando bash:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Ao definir a cadeia de conexão na `AZURE_STORAGE_CONNECTION_STRING` variável de ambiente, você pode acessar sua conta de armazenamento sem precisar fornecer autenticação a cada vez.

### <a name="query-the-storage-queue"></a>Consultar a fila de armazenamento

Você pode usar o [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) comando para exibir as filas de armazenamento em sua conta, como no exemplo a seguir:

```azurecli-interactive
az storage queue list --output tsv
```

A saída desse comando inclui uma fila chamada `outqueue`, que é a fila que foi criada quando a função foi executada.

Em seguida, use [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) o comando para exibir as mensagens nesta fila, como neste exemplo:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A cadeia de caracteres retornada deve ser igual à mensagem que você enviou para testar a função.

> [!NOTE]  
> O exemplo anterior decodifica a cadeia de caracteres retornada de Base64. Isso ocorre porque as associações de armazenamento de filas gravam e lêem do armazenamento do Azure como cadeias de [caracteres Base64](functions-bindings-storage-queue.md#encoding).

Agora é hora de republicar o aplicativo de funções atualizado no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Novamente, você pode usar a ondulação ou um navegador para testar a função implantada. Como antes, acrescente a cadeia de `&name=<yourname>` caracteres de consulta à URL, como neste exemplo:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Você pode [examinar a mensagem da fila de armazenamento](#query-the-storage-queue) para verificar se a associação de saída gera novamente uma nova mensagem na fila.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Você atualizou sua função disparada por HTTP para gravar dados em uma fila de armazenamento. Para saber mais sobre como desenvolver Azure Functions com Python, confira o [Guia do desenvolvedor Azure Functions Python](functions-reference-python.md) e [Azure Functions gatilhos e associações](functions-triggers-bindings.md). Para obter exemplos de projetos de funções completos no Python, consulte os [exemplos de funções do Python](/samples/browse/?products=azure-functions&languages=python). 

Em seguida, você deve habilitar o monitoramento de Application Insights para seu aplicativo de funções:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/