---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329571"
---
### <a name="set-the-storage-account-connection"></a>Definir a conexão da conta de armazenamento

Abra o arquivo local. Settings. JSON e copie o valor de `AzureWebJobsStorage`, que é a cadeia de conexão da conta de armazenamento. Defina a variável de ambiente `AZURE_STORAGE_CONNECTION_STRING` para a cadeia de conexão usando este comando bash:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Ao definir a cadeia de conexão na variável de ambiente `AZURE_STORAGE_CONNECTION_STRING`, você pode acessar sua conta de armazenamento sem precisar fornecer autenticação a cada vez.