---
title: incluir ficheiro
description: incluir ficheiro
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: d81b6f5367efa92c9249956faa058441edf98561
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92756096"
---
### <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo utilizador

Crie uma identidade chamada *myACRTasksId* na sua subscrição utilizando o comando [de criação de identidade az.][az-identity-create] Pode utilizar o mesmo grupo de recursos que usou anteriormente para criar um registo de contentores, ou um outro.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Para configurar a identidade atribuída ao utilizador nos seguintes passos, utilize o comando [de demonstração de identidade az][az-identity-show] para armazenar o ID de recursos da identidade, iD principal e identificação do cliente em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
