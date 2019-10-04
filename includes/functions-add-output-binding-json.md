---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 64a1062a8b73768a334277eafb663a7d2d5dd59a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838964"
---
Os atributos de associação são definidos diretamente no arquivo function. JSON. Dependendo do tipo de associação, podem ser necessárias propriedades adicionais. A [configuração de saída de fila](../articles/azure-functions/functions-bindings-storage-queue.md#output---configuration) descreve os campos necessários para uma associação de fila de armazenamento do Azure. A extensão facilita a adição de associações ao arquivo function. JSON. 

Para criar uma associação, clique com o botão direito do mouse (Ctrl + clique em macOS) o arquivo `function.json` na pasta HttpTrigger e escolha **Adicionar associação...** . Siga os prompts para definir as seguintes propriedades de associação para a nova associação:

| Mensagem | Value | Descrição |
| -------- | ----- | ----------- |
| **Selecionar direção da Associação** | `out` | A associação é uma associação de saída. |
| **Selecionar associação com direção...** | `Azure Queue Storage` | A associação é uma associação de fila de armazenamento do Azure. |
| **O nome usado para identificar essa associação em seu código** | `msg` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **A fila para a qual a mensagem será enviada** | `outqueue` | O nome da fila na qual a associação é gravada. Quando o *QueueName* não existe, a associação o cria no primeiro uso. |
| **Selecione configuração em "local. setting. JSON"** | `AzureWebJobsStorage` | O nome de uma configuração de aplicativo que contém a cadeia de conexão para a conta de armazenamento. A configuração `AzureWebJobsStorage` contém a cadeia de conexão para a conta de armazenamento que você criou com o aplicativo de funções. |

Uma associação é adicionada à matriz `bindings` em seu arquivo function. JSON, que agora deve ser semelhante ao exemplo a seguir:

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