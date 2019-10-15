---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: ca576290ea511dc54b89ecebef72ca2a42e9169f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329569"
---
### <a name="query-the-storage-queue"></a>Consultar a fila de armazenamento

Você pode usar o comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) para exibir as filas de armazenamento em sua conta, como no exemplo a seguir:

```azurecli-interactive
az storage queue list --output tsv
```

A saída desse comando inclui uma fila chamada `outqueue`, que é a fila que foi criada quando a função foi executada.

Em seguida, use o comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) para exibir as mensagens nesta fila, como neste exemplo:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A cadeia de caracteres retornada deve ser igual à mensagem que você enviou para testar a função.

> [!NOTE]  
> O exemplo anterior decodifica a cadeia de caracteres retornada de Base64. Isso ocorre porque as associações de armazenamento de filas gravam e lêem do armazenamento do Azure como [cadeias de caracteres Base64](../articles/azure-functions/functions-bindings-storage-queue.md#encoding).