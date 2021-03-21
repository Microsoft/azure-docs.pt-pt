---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93425167"
---
## <a name="deploy-the-function-project-to-azure"></a>Implementar o projeto de função para Azure

Depois de ter criado com sucesso a sua aplicação de função em Azure, está agora pronto para implementar o seu projeto de funções locais utilizando o comando [de publicação do func azure functionapp.](../articles/azure-functions/functions-run-local.md#project-file-deployment)  

No exemplo seguinte, `<APP_NAME>` substitua-a pelo nome da sua aplicação.

```console
func azure functionapp publish <APP_NAME>
```

O comando de publicação apresenta resultados semelhantes à seguinte saída (truncada para a simplicidade):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
