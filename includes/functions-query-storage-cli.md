---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77474267"
---
### <a name="query-the-storage-queue"></a>Consulta da fila de armazenamento

Pode utilizar [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) o comando para visualizar as filas de armazenamento na sua conta, como no seguinte exemplo:

```azurecli-interactive
az storage queue list --output tsv
```

A saída deste comando inclui `outqueue`uma fila chamada , que é a fila que foi criada quando a função funcionou.

Em seguida, [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) use o comando para ver as mensagens nesta fila, como neste exemplo:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A corda devolvida deve ser a mesma que a mensagem que enviou para testar a função.

> [!NOTE]  
> O exemplo anterior descodifica a corda devolvida da base64. Isto porque as encadernações de armazenamento de fila escrevem e lêem a partir do Armazenamento Azure como [cordas base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).