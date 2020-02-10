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
ms.openlocfilehash: ceda7bd6bd165df1eece555c6ce8a9a6c863b2c1
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112274"
---
### <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo utilizador

Crie uma identidade chamada *myACRTasksId* na sua subscrição utilizando o comando de criação de [identidade az.][az-identity-create] Pode utilizar o mesmo grupo de recursos que usou anteriormente para criar um registo de contentores, ou outro.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Para configurar a identidade atribuída ao utilizador nos seguintes passos, utilize o comando de show de [identidade az][az-identity-show] para armazenar o ID de recursos da identidade, id principal e ID do cliente em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show