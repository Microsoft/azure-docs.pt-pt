---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78262100"
---
### <a name="set-the-storage-account-connection"></a>Definir a ligação da conta de armazenamento

Abra o ficheiro local.settings.json e `AzureWebJobsStorage`copie o valor de , que é a cadeia de ligação à conta de armazenamento. Desloque a `AZURE_STORAGE_CONNECTION_STRING` variável ambiental à cadeia de ligação utilizando este comando Bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Quando definir a cadeia `AZURE_STORAGE_CONNECTION_STRING` de ligação na variável ambiente, pode aceder à sua conta de Armazenamento sem ter de fornecer a autenticação de cada vez.