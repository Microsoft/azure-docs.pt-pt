---
title: Início rápido - criar um registo privado do Docker no Azure - CLI do Azure
description: Aprenda rapidamente a criar um registo do contentor do Docker com a CLI do Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 24bdd52673c65d039166dc28f9f0a0a784569a1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787393"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Início rápido: Criar um registo de contentor privado com a CLI do Azure

O Azure Container Registry é um serviço de registo do contentor Docker gerido, utilizado para armazenar imagens de contentor do Docker privadas. Este guia detalha a criação de uma instância do Azure Container Registry com a CLI do Azure. Em seguida, utilize os comandos do Docker para enviar por push uma imagem de contentor para o registro e finalmente extrair e executar a imagem a partir do registo.

Este início rápido requer que está a executar a CLI do Azure (versão 2.0.55 ou recomendados mais tarde). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Também tem de ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

Uma vez que o Azure Cloud Shell não inclui todos os componentes do Docker necessários (o daemon `dockerd`), não é possível utilizar o Cloud Shell para este guia de início rápido.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create][az-group-create]. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Neste início rápido de criar uma *básica* registo, o que é uma opção com otimização de custos para os desenvolvedores a aprender sobre o Azure Container Registry. Para obter detalhes sobre os escalões de serviço disponíveis, consulte [SKUs do registo de contentor][container-registry-skus].

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

Tome nota do `loginServer` na saída, que é o nome do registo completamente qualificado (em minúsculas). Em todo o resto deste início rápido, `<acrName>` é um marcador de posição para o nome do registo de contentor.

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão registo. Para tal, utilize o comando [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

O comando devolve uma mensagem de `Login Succeeded` depois de estar concluído.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contentor

O exemplo seguinte lista os repositórios no seu registo:

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```
Result
----------------
hello-world
```

O exemplo a seguir lista as etiquetas no **hello-world** repositório.

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

Quando já não for necessário, pode utilizar o [eliminação do grupo de az] [ az-group-delete] comando para remover o grupo de recursos, o registo de contentor e as imagens de contentor armazenadas nele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um Azure Container Registry com a CLI do Azure, enviou uma imagem de contentor para o registo e extraídos e executou a imagem do registo. Avance para os tutoriais de registo de contentor do Azure para uma análise mais profunda ACR.

> [!div class="nextstepaction"]
> [Tutoriais de registo de contentor do Azure][container-registry-tutorial-quick-task]

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
