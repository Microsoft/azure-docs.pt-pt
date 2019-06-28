---
title: Adicione um enlace de fila de armazenamento do Azure à sua função de Python
description: Saiba como adicionar uma saída de fila de armazenamento do Azure para sua função de Python com a CLI do Azure e as ferramentas de núcleo das funções de ligação.
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
ms.openlocfilehash: 4ae22a5cd6ad044a86db88986daf9cc7c05c00a2
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342317"
---
# <a name="add-an-azure-storage-queue-binding-to-your-function"></a>Adicione um enlace de fila de armazenamento do Azure à sua função

As funções do Azure permite-lhe ligar os serviços do Azure e outros recursos para as funções sem ter de escrever seu próprio código de integração. Estes *enlaces*, que representam a entrada e saída, são declarados dentro da definição de função. Dados a partir de ligações são fornecidos para a função como parâmetros. Um acionador é um tipo especial de enlace de entrada. Enquanto uma função tem apenas um acionador, ele pode ter várias entradas e ligações de saída. Para obter mais informações, consulte [acionadores de funções do Azure e conceitos de enlaces](functions-triggers-bindings.md).

Este artigo mostra-lhe como integrar a função que criou o [artigo de início rápido anterior](functions-create-first-function-python.md) com uma fila de armazenamento do Azure. O enlace de saída que adicionar a esta função escreve dados de solicitação HTTP para uma mensagem na fila. 

A maioria dos enlaces necessitam de uma cadeia de ligação armazenadas que as funções utilizam para aceder ao serviço ligado. Para tornar mais fácil, utilize a conta de armazenamento que criou com a sua aplicação de função. A ligação a esta conta já armazenada numa definição com o nome de aplicação `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, conclua os passos na [parte 1 do início rápido Python](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Transferir as definições de aplicação de função

O artigo de início rápido anterior, criou uma aplicação de funções no Azure, juntamente com uma conta de armazenamento. A cadeia de ligação para esta conta é armazenada em segurança nas definições da aplicação no Azure. Neste artigo, escrever mensagens numa fila de armazenamento na mesma conta. Para ligar à sua conta de armazenamento ao executar localmente a função, tem de transferir as definições da aplicação para o ficheiro Settings. Execute o seguinte comando a ferramentas de núcleo de funções do Azure para transferir as definições para Settings, substituindo `<APP_NAME>` com o nome da sua aplicação de função do artigo anterior:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Poderá ter de iniciar sessão na sua conta do Azure.

> [!IMPORTANT]  
> Porque contém segredos, o arquivo Settings nunca seja publicado, e devem ser excluído no controle da fonte.

Terá do valor `AzureWebJobsStorage`, que é a cadeia de ligação de conta de armazenamento. Utilize esta ligação para verificar se o enlace de saída funciona conforme esperado.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em funções, cada tipo de ligação requer um `direction`, `type`e um único `name` ser definidos no ficheiro Function. Dependendo do tipo de ligação, as propriedades adicionais podem ser necessárias. O [configuração da fila de saída](functions-bindings-storage-queue.md#output---configuration) descreve os campos necessários para um enlace de fila de armazenamento do Azure.

Para criar uma ligação, é possível adicionar um objeto de configuração de ligação para o `function.json` ficheiro. Edite o ficheiro de Function em sua pasta de HttpTrigger para adicionar um objeto para o `bindings` matriz que tem as seguintes propriedades:

| Propriedade | Value | Descrição |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Nome que identifica o parâmetro de ligação referenciado no seu código. |
| **`type`** | `queue` | A associação é um enlace de fila de armazenamento do Azure. |
| **`direction`** | `out` | A associação é um enlace de saída. |
| **`queueName`** | `outqueue` | O nome da fila na qual o enlace escreve. Quando o *queueName* não existir, o enlace cria-o na primeira utilização. |
| **`connection`** | `AzureWebJobsStorage` | O nome de uma definição de aplicação que contém a cadeia de ligação para a conta de armazenamento. O `AzureWebJobsStorage` definição contém a cadeia de ligação para a conta de armazenamento que criou com a aplicação de funções. |

O ficheiro de Function deve agora ter um aspeto semelhante ao seguinte exemplo:

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

Assim que estiver configurado, pode começar a utilizar o `name` do enlace para acessá-lo como um atributo de método na assinatura de função. No exemplo a seguir `msg` é uma instância do [ `azure.functions.InputStream class` ](/python/api/azure-functions/azure.functions.httprequest).

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

Ao utilizar um enlace de saída, não tem de utilizar o código do SDK de armazenamento do Azure para a autenticação, obter uma referência de fila, ou escrever dados. A saída de tempo de execução e a fila de funções enlace executar essas tarefas para.

## <a name="run-the-function-locally"></a>Executar localmente a função

Como antes, utilize o seguinte comando para iniciar o runtime das funções localmente:

```bash
func host start
```

> [!NOTE]  
> Uma vez que o artigo anterior tinha-o a ativar os pacotes de extensão no Host. JSON, o [extensão de ligação de armazenamento](functions-bindings-storage-blob.md#packages---functions-2x) foi transferido e instalado para durante o arranque.

Copie o URL da função `HttpTrigger` do resultado do runtime e cole-o na barra de endereço do browser. Anexe a cadeia de consulta `?name=<yourname>` a este URL e execute o pedido. Deverá ver a mesma resposta no browser, tal como fez no artigo anterior.

Desta vez, o enlace de saída também cria uma fila com o nome `outqueue` no seu armazenamento de conta e adiciona uma mensagem com esta mesma cadeia de caracteres.

Em seguida, vai utilizar a CLI do Azure para ver a nova fila e verificar se uma mensagem foi adicionada. Também pode ver a sua fila com o [Explorador de armazenamento do Microsoft Azure][Azure Storage Explorer] ou no [portal do Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Definir a ligação de conta de armazenamento

Abra o ficheiro Settings e copie o valor de `AzureWebJobsStorage`, que é a cadeia de ligação de conta de armazenamento. Definir o `AZURE_STORAGE_CONNECTION_STRING` variável de ambiente para a cadeia de ligação com o seguinte comando do Bash:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Com a cadeia de ligação definida `AZURE_STORAGE_CONNECTION_STRING` variável de ambiente, pode acessar sua conta de armazenamento sem ter de fornecer autenticação cada vez.

### <a name="query-the-storage-queue"></a>Consulta a fila de armazenamento

Pode utilizar o [ `az storage queue list` ](/cli/azure/storage/queue#az-storage-queue-list) comando para ver as filas de armazenamento na sua conta, como no exemplo seguinte:

```azurecli-interactive
az storage queue list --output tsv
```

O resultado deste comando inclui uma fila com o nome `outqueue`, que é a fila que foi criada quando a função foi executado.

Em seguida, utilize o [ `az storage message peek` ](/cli/azure/storage/message#az-storage-message-peek) comando para ver as mensagens nesta fila, como no exemplo seguinte.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A cadeia de caracteres retornada deve ser o mesmo que a mensagem enviada para testar a função.

> [!NOTE]  
> O exemplo anterior descodifica a cadeia de caracteres retornada de base64. Isto acontece porque os enlaces de armazenamento de filas gravar e leem a partir do armazenamento do Azure como [cadeias de caracteres de base64](functions-bindings-storage-queue.md#encoding).

Agora, é hora de voltar a publicar a aplicação de função atualizada para o Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Mais uma vez, pode utilizar cURL ou um browser para testar a função implementada. Como antes, anexe a cadeia de consulta `&name=<yourname>` para o URL, como no exemplo seguinte:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Pode [Examine a mensagem de fila de armazenamento](#query-the-storage-queue) para verificar que a saída novamente a vinculação gera uma nova mensagem na fila.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Atualizou a função de acionada por HTTP para escrever dados para uma fila de armazenamento. Para saber mais sobre o desenvolvimento das funções do Azure com o Python, veja a [guia de programadores de Python de funções do Azure](functions-reference-python.md) e [as funções do Azure acionadores e enlaces](functions-triggers-bindings.md).

Em seguida, deve ativar a monitorização do Application Insights para a sua aplicação de função:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/