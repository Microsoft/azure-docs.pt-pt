---
title: Tutorial - preparar um registo de contentor do Azure Container Instances
description: Tutorial do Azure Container Instances parte 2 de 3 - Preparar um registo de contentor do Azure e enviar uma imagem
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c1a4313f9a8174b9ea6e6cff694b9a0a9cf395d1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538158"
---
# <a name="tutorial-deploy-an-azure-container-registry-and-push-a-container-image"></a>Tutorial: Implementar um Azure container registry e enviar uma imagem de contentor

Esta é a parte dois de um tutorial de três partes. Na [Parte um](container-instances-tutorial-prepare-app.md) do tutorial criou uma imagem de contentor Docker para uma aplicação Web Node.js. Neste tutorial, vai enviar a imagem para o Azure Container Registry. Se ainda não tiver criado a imagem de contentor, regresse ao [Tutorial 1 – Criar imagem de contentor](container-instances-tutorial-prepare-app.md).

O Azure Container Registry é o seu registo de Docker privado no Azure. Neste tutorial, vai criar uma instância do Azure Container Registry na sua subscrição e enviar por push a imagem de contentor criada anteriormente. Neste artigo, parte dois da série, vai:

> [!div class="checklist"]
> * Criar uma instância do Azure Container Registry
> * Marcar uma imagem de contentor para o seu registo de contentor do Azure
> * Carregar a imagem para o seu registo

No próximo artigo, o último da série, vai implementar o contentor do seu registo privado para o Azure Container Instances.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Criar um registo de contentor do Azure

Antes de criar o registo de contentor, precisa de um *grupo de recursos* no qual o implementar. Um grupo de recursos é uma coleção lógica na qual todos os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create][az-group-create]. No exemplo seguinte, é criado um grupo de recursos designado *myResourceGroup* na região *eualeste*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Depois de criar o grupo de recursos, crie um registo de contentor do Azure com o comando [az acr create][az-acr-create]. O nome do registo de contentor tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. Substitua `<acrName>` por um nome exclusivo para o seu registo:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Segue-se um resultado de exemplo para um novo registo de contentor do Azure com o nome *mycontainerregistry082* (aqui apresentado como truncado):

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
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

O comando devolve `Login Succeeded` depois de estar concluído:

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>Imagem de contentor

Para enviar por push uma imagem de contentor para um registo privado, como o Azure Container Registry, primeiro tem de marcar a imagem com o nome completo do servidor de início de sessão no registo.

Em primeiro lugar, obtenha o nome completo do servidor de início de sessão do seu registo de contentor do Azure. Execute o seguinte comando [az acr show][az-acr-show] e substitua `<acrName>` pelo nome do registo que acabou de criar:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Por exemplo, se o registo tiver o nome *mycontainerregistry082*:

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

Agora, apresente a lista das suas imagens locais com o comando [docker images][docker-images]:

```bash
docker images
```

Juntamente com quaisquer outras imagens que tiver no seu computador, deverá ver a imagem *aci-tutorial-app* que incorporou no [tutorial anterior](container-instances-tutorial-prepare-app.md):

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Marque a imagem *aci-tutorial-app* com o loginServer do seu registo de contentor. Além disso, adicione a etiqueta `:v1` ao fim do nome da imagem para indicar o número da versão da imagem. Substitua `<acrLoginServer>` pelo resultado do comando [az acr show][az-acr-show] que executou anteriormente.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Execute `docker images` novamente para verificar a operação de marcação:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Enviar imagens para o Azure Container Registry

Agora que já marcou a imagem *aci-tutorial-app* com o nome completo do servidor de início de sessão do seu registo privado, pode enviá-la por push para o registo com o comando [docker push][docker-push]. Substitua `<acrLoginServer>` pelo nome completo de servidor de início de sessão que obteve no passo anterior.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

A operação `push` deverá demorar alguns segundos a alguns minutos, consoante a sua ligação à Internet, e os resultados são semelhantes ao seguinte:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Listar imagens no Azure Container Registry

Para certificar-se de que a imagem que enviou por push está, de facto, no seu registo de contentor do Azure, liste as imagens no registo com o comando [az acr repository list][az-acr-repository-list]. Substitua `<acrName>` pelo nome do seu registo de contentor.

```azurecli
az acr repository list --name <acrName> --output table
```

Por exemplo:

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

Para ver as *etiquetas* de uma imagem específica, utilize o comando [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Deverá ver um resultado semelhante ao seguinte:

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, preparou um registo de contentor do Azure para utilização com o Azure Container Instances e enviou por push uma imagem de contentor para o registo. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Implementou uma instância do Azure Container Registry
> * Marcou uma imagem de contentor para o Azure Container Registry
> * Carregou uma imagem para o Azure Container Registry

Avance para o próximo tutorial para saber como implementar o contentor para o Azure através do Azure Container Instances:

> [!div class="nextstepaction"]
> [Implementar um contentor no Azure Container Instances](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
