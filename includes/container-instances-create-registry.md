---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 2b1d9b7f9ff07a3e0c7745191decc3e82181553e
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708024"
---
## <a name="create-azure-container-registry"></a>Criar um registo de contentor do Azure

Antes de criar o registo de contentor, precisa de um *grupo de recursos* no qual o implementar. Um grupo de recursos é uma coleção lógica na qual todos os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create][az-group-create]. No exemplo seguinte, é criado um grupo de recursos designado *myResourceGroup* na região *eualeste*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Depois de criar o grupo de recursos, crie um registo de contentor do Azure com o comando [az acr create][az-acr-create]. O nome do registo de contentor tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. Substitua `<acrName>` por um nome exclusivo para o seu registo:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Aqui está a saída parcial para um novo registo de contentores Azure chamado *mycontainerregistry082*:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

O resto do tutorial refere-se a `<acrName>` como um marcador de posição para o nome do registo de contentor que escolheu neste passo.

## <a name="log-in-to-container-registry"></a>Iniciar sessão no registo do contentor

Tem de iniciar sessão na sua instância do Azure Container Registry antes de enviar imagens para a mesma. Utilize o comando [az acr login][az-acr-login] para concluir a operação. Tem de fornecer o nome exclusivo que escolheu para o registo de contentor quando o criou.

```azurecli
az acr login --name <acrName>
```

Por exemplo:

```azurecli
az acr login --name mycontainerregistry082
```

O comando devolve `Login Succeeded` depois de estar concluído:

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create