---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: b118da6b751bc7a1e29ceef10c91dc5e9e3659c2
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474185"
---
Os atributos de ligação são definidos diretamente no ficheiro função.json. Dependendo do tipo de encadernação, podem ser necessárias propriedades adicionais. A [configuração](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) de saída da fila descreve os campos necessários para uma ligação de fila de armazenamento Azure. A extensão facilita a adição de encadernações ao ficheiro função.json. 

Para criar uma ligação, clique à direita (Ctrl+clique no macOS) o ficheiro `function.json` na sua pasta HttpTrigger e escolha **Adicionar a ligação...** . Siga as instruções para definir as seguintes propriedades vinculativas para a nova ligação:

| Mensagem | Valor | Descrição |
| -------- | ----- | ----------- |
| **Selecione direção de ligação** | `out` | A ligação é uma ligação de saída. |
| **Selecione encadernação com direção...** | `Azure Queue Storage` | A encadernação é uma ligação de fila de armazenamento Azure. |
| **O nome usado para identificar esta ligação no seu código** | `msg` | Nome que identifique o parâmetro de ligação referenciado no seu código. |
| **A fila para a qual a mensagem será enviada** | `outqueue` | O nome da fila a que o encadernação escreve. Quando o nome da *fila* não existe, a ligação cria-o na primeira utilização. |
| **Selecione a definição de "local.setting.json"** | `AzureWebJobsStorage` | O nome de uma definição de aplicação que contém a cadeia de ligação para a conta de armazenamento. A definição `AzureWebJobsStorage` contém a cadeia de ligação para a conta de Armazenamento que criou com a aplicação de função. |

Uma ligação é adicionada ao conjunto de `bindings` no seu ficheiro função.json, que deve agora parecer o seguinte exemplo:

```json
{
   ...

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