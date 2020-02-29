---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191071"
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

Uma ligação é adicionada ao conjunto `bindings` na sua função.json, que deve parecer o seguinte:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::