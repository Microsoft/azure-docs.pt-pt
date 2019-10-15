---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329541"
---
Você já criou um aplicativo de funções no Azure, junto com a conta de armazenamento necessária. A cadeia de conexão para essa conta é armazenada com segurança nas configurações do aplicativo no Azure. Neste artigo, você grava mensagens em uma fila de armazenamento na mesma conta. Para se conectar à sua conta de armazenamento ao executar a função localmente, você deve baixar as configurações do aplicativo para o arquivo local. Settings. JSON. 

Na raiz do projeto, execute o seguinte comando Azure Functions Core Tools para baixar as configurações para local. Settings. JSON, substituindo `<APP_NAME>` pelo nome do seu aplicativo de funções do artigo anterior:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Talvez seja necessário entrar em sua conta do Azure.

> [!IMPORTANT]  
> Esse comando substitui todas as configurações existentes por valores de seu aplicativo de funções no Azure.  
>
> Como ele contém segredos, o arquivo local. Settings. JSON nunca é publicado e deve ser excluído do controle do código-fonte.  
> 

Você precisa do valor `AzureWebJobsStorage`, que é a cadeia de conexão da conta de armazenamento. Use essa conexão para verificar se a associação de saída funciona conforme o esperado.
