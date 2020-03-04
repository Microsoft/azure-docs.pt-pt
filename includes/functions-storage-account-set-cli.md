---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262100"
---
### <a name="set-the-storage-account-connection"></a>Definir a ligação da conta de armazenamento

Abra o ficheiro local.settings.json e copie o valor de `AzureWebJobsStorage`, que é o fio de ligação da conta de armazenamento. Detete a variável ambiente `AZURE_STORAGE_CONNECTION_STRING` na cadeia de ligação utilizando este comando Bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Quando definir a cadeia de ligação na variável ambiente `AZURE_STORAGE_CONNECTION_STRING`, pode aceder à sua conta de Armazenamento sem ter de fornecer a autenticação de cada vez.