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
ms.openlocfilehash: 70bd79ef944e5918d750a130bd2e2b2c2b656bf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781149"
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
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
