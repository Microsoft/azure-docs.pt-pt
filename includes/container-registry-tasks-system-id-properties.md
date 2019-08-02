---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 5cca18b881250ce99df35d681bec6091ea4a27b9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642101"
---
Na saída do comando, a `identity` seção mostra que a identidade do `SystemAssigned` tipo está definida na tarefa. O `principalId` é a ID da entidade de serviço da identidade:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 
Use o comando [AZ ACR Task show][az-acr-task-show] para armazenar a PrincipalId em uma variável, para usar em comandos posteriores:

```azurecli
principalID=$(az acr task show --name dockerhubtask --registry myregistry --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
