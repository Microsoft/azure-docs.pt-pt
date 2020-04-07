---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673324"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Recupere a cadeia de ligação de armazenamento azure

Anteriormente, criou uma conta de Armazenamento Azure para utilização pela aplicação de funções. A cadeia de ligação para esta conta é armazenada de forma segura nas definições da aplicação em Azure. Ao descarregar a definição no ficheiro *local.settings.json,* pode utilizar essa ligação escrever para uma fila de armazenamento na mesma conta ao executar a função localmente. 

1. A partir da raiz do projeto, execute o seguinte comando, substituindo `<app_name>` pelo nome da sua aplicação de função a partir do quickstart anterior. Este comando irá substituir quaisquer valores existentes no ficheiro.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Abra *local.settings.json* e localize o valor nomeado, `AzureWebJobsStorage`que é a cadeia de ligação da conta de armazenamento. Usa o `AzureWebJobsStorage` nome e a corda de ligação noutras secções deste artigo.

> [!IMPORTANT]
> Como *local.settings.json contém segredos descarregados* do Azure, exclua sempre este ficheiro do controlo de origem. O ficheiro *.gitignore* criado com um projeto de funções locais exclui o ficheiro por defeito.