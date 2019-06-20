---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184044"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Implementar o projeto da aplicação de funções no Azure

Depois da aplicação de funções é criada no Azure, pode utilizar o [ `func azure functionapp publish` ](../articles/azure-functions/functions-run-local.md#project-file-deployment) comando de ferramentas de núcleo para implementar o código do projeto no Azure. No comando seguinte, substitua `<APP_NAME>` com o nome da sua aplicação do passo anterior.

```bash
func azure functionapp publish <APP_NAME>
```

Aparece um resultado semelhante ao seguinte, que foi truncado para facilitar a leitura.

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Copie o valor de URL invocar para HttpTrigger, o que agora pode utilizar para testar a sua função no Azure. O URL contém um `code` valor de cadeia de caracteres de consulta é a chave de função. Esta chave torna difícil para outras pessoas chamar o ponto final de Acionador HTTP no Azure.