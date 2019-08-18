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
ms.openlocfilehash: e2d63ab38bad341400538c5079fee22737cf0b8e
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562939"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Implementar o projeto da aplicação de funções no Azure

Depois que o aplicativo de funções é criado no Azure, você pode [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) usar o comando ferramentas principais para implantar o código do projeto no Azure. Nestes exemplos, substitua `<APP_NAME>` pelo nome do seu aplicativo da etapa anterior.

### <a name="c--javascript"></a>C\# /JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="python"></a>Python

```command
func azure functionapp publish <APP_NAME> --build remote
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Você verá uma saída semelhante à seguinte, que foi truncada para facilitar a leitura:

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

Copie o `Invoke url` valor para seu `HttpTrigger`, que agora você pode usar para testar sua função no Azure. A URL contém um `code` valor de cadeia de caracteres de consulta que é sua chave de função. Essa chave torna difícil para outras pessoas chamarem seu ponto de extremidade de gatilho HTTP no Azure.