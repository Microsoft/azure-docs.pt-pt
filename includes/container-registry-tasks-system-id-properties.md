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
ms.openlocfilehash: 700dbfde3be2f24eb57acbdeb9d2841ef2bdfe44
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112296"
---
Na saída de comando, a secção `identity` mostra uma identidade de tipo `SystemAssigned` é definida na tarefa. O `principalId` é a identificação principal da identidade de tarefa:

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
Utilize o comando de show de [tarefas az acr][az-acr-task-show] para armazenar o principado numa variável, para utilizar em comandos posteriores. Substitua o nome da sua tarefa e o seu registo no seguinte comando:

```azurecli
principalID=$(az acr task show --name mytask --registry myregistry --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
