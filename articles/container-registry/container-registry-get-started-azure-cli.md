---
title: Quickstart - Create registry - Azure CLI
description: Aprenda rapidamente a criar um registo do contentor do Docker com a CLI do Azure.
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 551a3659feb39943c9f794484abb6f2da4367f39
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455165"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Quickstart: Create a private container registry using the Azure CLI

O Azure Container Registry é um serviço de registo do contentor Docker gerido, utilizado para armazenar imagens de contentor do Docker privadas. Este guia detalha a criação de uma instância do Azure Container Registry com a CLI do Azure. Then, use Docker commands to push a container image into the registry, and finally pull and run the image from your registry.

This quickstart requires that you are running the Azure CLI (version 2.0.55 or later recommended). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli].

Também tem de ter o Docker instalado localmente. Docker provides packages that easily configure Docker on any [macOS][docker-mac], [Windows][docker-windows], or [Linux][docker-linux] system.

Uma vez que o Azure Cloud Shell não inclui todos os componentes do Docker necessários (o daemon `dockerd`), não é possível utilizar o Cloud Shell para este guia de início rápido.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create][az-group-create]. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Create a container registry (Criar um registo de contentores)

In this quickstart you create a *Basic* registry, which is a cost-optimized option for developers learning about Azure Container Registry. For details on available service tiers, see [Container registry SKUs][container-registry-skus].

Crie uma instância do ACR com o comando [az acr create][az-acr-create]. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. No exemplo seguinte, é utilizado *myContainerRegistry007*. Atualize para um valor exclusivo.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Quando o registo é criado, o resultado é semelhante ao seguinte:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
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

Take note of `loginServer` in the output, which is the fully qualified registry name (all lowercase). Em todo o resto deste início rápido, `<acrName>` é um marcador de posição para o nome do registo de contentor.

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Before pushing and pulling container images, you must log in to the registry. Para tal, utilize o comando [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

O comando devolve uma mensagem de `Login Succeeded` depois de estar concluído.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contentor

The following example lists the repositories in your registry:

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```
Result
----------------
hello-world
```

The following example lists the tags on the **hello-world** repository.

```azurecli
az acr repository show-tags --name <acrName> --repository hello-world --output table
```

Saída:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar recursos

When no longer needed, you can use the [az group delete][az-group-delete] command to remove the resource group, the container registry, and the container images stored there.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you created an Azure Container Registry with the Azure CLI, pushed a container image to the registry, and pulled and ran the image from the registry. Continue to the Azure Container Registry tutorials for a deeper look at ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry tutorials][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
