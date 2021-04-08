---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "78262100"
---
### <a name="set-the-storage-account-connection"></a>Definir a ligação da conta de armazenamento

Abra a local.settings.jsno ficheiro e copie o valor de , que é a cadeia de `AzureWebJobsStorage` ligação da conta de Armazenamento. Desaprote `AZURE_STORAGE_CONNECTION_STRING` a variável ambiente para a cadeia de ligação utilizando este comando Bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Quando definir a cadeia de ligação na `AZURE_STORAGE_CONNECTION_STRING` variável ambiente, pode aceder à sua conta de Armazenamento sem ter de fornecer autenticação de cada vez.