---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493955"
---
O Visual Studio Code permite-lhe adicionar encadernações à sua function.jsno ficheiro, seguindo um conveniente conjunto de pedidos. 

Para adicionar uma ligação, abra a palete de comando (F1) e o tipo **Azure Funções: adicione encadernação...**, escolha a função para a nova encadernação e, em seguida, siga as indicações, que variam dependendo do tipo de encadernação adicionada à função. 

Seguem-se os exemplos de indicações para definir uma nova ligação à saída de armazenamento:

| Prompt | Valor | Descrição |
| -------- | ----- | ----------- |
| **Selecione direção de encadernação** | `out` | A ligação é uma ligação de saída. |
| **Selecione encadernação com direção** | `Azure Queue Storage` | A encadernação é uma encadernação de fila de armazenamento Azure. |
| **O nome usado para identificar esta ligação no seu código** | `msg` | Nome que identifica o parâmetro de ligação referenciado no seu código. |
| **A fila para a qual a mensagem será enviada** | `outqueue` | O nome da fila a que a ligação escreve. Quando o nome da *fila* não existe, a ligação cria-o na primeira utilização. |
| **Selecione a definição de "local.settings.json"** | `MyStorageConnection` | O nome de uma definição de aplicação que contém o fio de ligação para a conta de armazenamento. A `AzureWebJobsStorage` definição contém a cadeia de ligação para a conta de armazenamento que criou com a aplicação de função. |

Também pode clicar à direita (Ctrl+clique no macOS) diretamente no **function.jsficheiro na** pasta de funções, selecione Adicionar **encadernação** e siga as mesmas indicações.

Neste exemplo, é adicionada a seguinte ligação à matriz do `bindings` seu function.jsem arquivo:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```