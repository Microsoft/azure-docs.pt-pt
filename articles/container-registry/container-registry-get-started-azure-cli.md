---
title: Quickstart - Criar registo - Azure CLI
description: Aprenda rapidamente a criar um registo do contentor do Docker com a CLI do Azure.
ms.topic: quickstart
ms.date: 06/12/2020
ms.custom: seodec18, H1Hack27Feb2017, mvc, devx-track-azurecli
ms.openlocfilehash: 226e50aec8f7c76a1b4c81d1a07d57583059ef0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020080"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Quickstart: Criar um registo privado de contentores utilizando o CLI Azure

O Azure Container Registry é um serviço de registo do contentor Docker gerido, utilizado para armazenar imagens de contentor do Docker privadas. Este guia detalha a criação de uma instância do Azure Container Registry com a CLI do Azure. Em seguida, use os comandos do Docker para empurrar uma imagem do contentor para o registo, e finalmente puxe e execute a imagem do seu registo.

Este arranque rápido requer que esteja a executar o Azure CLI (versão 2.0.55 ou posteriormente recomendado). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Também tem de ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

Uma vez que o Azure Cloud Shell não inclui todos os componentes do Docker necessários (o daemon `dockerd`), não é possível utilizar o Cloud Shell para este guia de início rápido.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create][az-group-create]. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentor

Neste quickstart você cria um registo *Básico,* que é uma opção otimizada para custos para desenvolvedores aprendendo sobre o Registo de Contentores Azure. Para obter mais informações sobre os níveis de serviço disponíveis, consulte [os níveis de serviço de registo de contentores][container-registry-skus].

Crie uma instância do ACR com o comando [az acr create][az-acr-create]. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. No exemplo seguinte, é utilizado *myContainerRegistry007*. Atualize para um valor exclusivo.

```azurecli
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry007 --sku Basic
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

Tome nota `loginServer` da saída, que é o nome de registo totalmente qualificado (todos os minúsculos). Ao longo do resto deste quickstart `<registry-name>` é um espaço reservado para o nome do registo do contentor, e `<login-server>` é um espaço reservado para o nome do servidor de login do registo.

## <a name="log-in-to-registry"></a>Iniciar sessão no registo

Antes de empurrar e puxar imagens do contentor, tem de iniciar sessão no registo. Para tal, utilize o comando [az acr login][az-acr-login]. Especifique apenas o nome do registo ao iniciar sessão com o Azure CLI. Não utilize o nome do servidor de login, que inclui um sufixo de domínio como `azurecr.io` . 

```azurecli
az acr login --name <registry-name>
```

Exemplo:

```azurecli
az acr login --name mycontainerregistry
```

O comando devolve uma mensagem de `Login Succeeded` depois de estar concluído.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contentor

O exemplo a seguir enumera os repositórios no seu registo:

```azurecli
az acr repository list --name <registry-name> --output table
```

Resultado:

```
Result
----------------
hello-world
```

O exemplo a seguir enumera as etiquetas no repositório **hello-world.**

```azurecli
az acr repository show-tags --name <registry-name> --repository hello-world --output table
```

Resultado:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar o comando de exclusão do [grupo az][az-group-delete] para remover o grupo de recursos, o registo do contentor e as imagens do contentor aí armazenadas.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um Registo de Contentores Azure com o CLI Azure, empurrou uma imagem de contentor para o registo, puxou e executou a imagem a partir do registo. Continue os tutoriais do Registo do Contentor de Azure para uma olhada mais profunda no ACR.

> [!div class="nextstepaction"]
> [Tutoriais do Registo de Contentores de Azure][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutoriais de tarefas de registo de contentores Azure][container-registry-tutorial-quick-task]

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
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
