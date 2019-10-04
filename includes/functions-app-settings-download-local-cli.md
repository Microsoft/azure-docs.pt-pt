---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839027"
---
Você já criou um aplicativo de funções no Azure, junto com a conta de armazenamento necessária. A cadeia de conexão para essa conta é armazenada com segurança nas configurações do aplicativo no Azure. Neste artigo, você grava mensagens em uma fila de armazenamento na mesma conta. Para se conectar à sua conta de armazenamento ao executar a função localmente, você deve baixar as configurações do aplicativo para o arquivo local. Settings. JSON. Execute o seguinte comando Azure Functions Core Tools para baixar as configurações para local. Settings. JSON, substituindo `<APP_NAME>` pelo nome do seu aplicativo de funções do artigo anterior:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Talvez seja necessário entrar em sua conta do Azure.

> [!IMPORTANT]  
> Como ele contém segredos, o arquivo local. Settings. JSON nunca é publicado e deve ser excluído do controle do código-fonte.

Você precisa do valor `AzureWebJobsStorage`, que é a cadeia de conexão da conta de armazenamento. Use essa conexão para verificar se a associação de saída funciona conforme o esperado.
