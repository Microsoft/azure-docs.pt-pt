---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77474267"
---
### <a name="query-the-storage-queue"></a>Consultar a fila de armazenamento

Pode utilizar o [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) comando para visualizar as filas de armazenamento na sua conta, como no seguinte exemplo:

```azurecli-interactive
az storage queue list --output tsv
```

A saída deste comando inclui uma fila chamada `outqueue` , que é a fila que foi criada quando a função foi desardenada.

Em seguida, use o [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) comando para ver as mensagens nesta fila, como neste exemplo:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A corda devolvida deve ser a mesma que a mensagem que enviou para testar a função.

> [!NOTE]  
> O exemplo anterior descodifica a cadeia devolvida da base64. Isto porque as ligações de armazenamento da fila escrevem e lêem do Azure Storage como [cordas base64.](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)