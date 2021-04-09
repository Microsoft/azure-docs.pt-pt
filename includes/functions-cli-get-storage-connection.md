---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673324"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Recupere a cadeia de conexão Azure Storage

Anteriormente, criou uma conta de Armazenamento Azure para ser utilizada pela aplicação de função. O fio de ligação desta conta é armazenado de forma segura nas definições de aplicações em Azure. Ao descarregar a definição para o *local.settings.jsno* ficheiro, pode utilizar essa ligação escrever para uma fila de Armazenamento na mesma conta quando executar a função localmente. 

1. A partir da raiz do projeto, executar o seguinte comando, substituindo `<app_name>` pelo nome da sua aplicação de função do quickstart anterior. Este comando substituirá os valores existentes no ficheiro.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Abra *local.settings.js* e localize o valor denominado `AzureWebJobsStorage` , que é a cadeia de ligação da conta de armazenamento. Utilize o nome `AzureWebJobsStorage` e a cadeia de ligação noutras secções deste artigo.

> [!IMPORTANT]
> Como *local.settings.jscontém* segredos descarregados do Azure, exclua sempre este ficheiro do controlo de origem. O ficheiro *.gitignore* criado com um projeto de funções locais exclui o ficheiro por predefinição.