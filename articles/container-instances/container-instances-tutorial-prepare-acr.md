---
title: Tutorial - Preparar registo de contentores para implantar imagem
description: Azure Container Instances tutorial parte 2 de 3 - Prepare um registo de contentores Azure e empurre uma imagem
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2eda960c53fc7ba851ffcfbe96bd8e9a48844910
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746931"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>Tutorial: Criar um registo de contentores Azure e empurrar uma imagem de contentor

Esta é a parte dois de um tutorial de três partes. Na [Parte um](container-instances-tutorial-prepare-app.md) do tutorial criou uma imagem de contentor Docker para uma aplicação Web Node.js. Neste tutorial, vai enviar a imagem para o Azure Container Registry. Se ainda não tiver criado a imagem de contentor, regresse ao [Tutorial 1 – Criar imagem de contentor](container-instances-tutorial-prepare-app.md).

O Azure Container Registry é o seu registo de Docker privado no Azure. Neste tutorial, parte dois da série, você:

> [!div class="checklist"]
> * Criar um caso de registo de contentores Azure com o CLI Azure
> * Marcar uma imagem de contentor para o seu registo de contentor do Azure
> * Carregar a imagem para o seu registo

No próximo artigo, o último da série, vai implementar o contentor do seu registo privado para o Azure Container Instances.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>Imagem de contentor

Para enviar por push uma imagem de contentor para um registo privado, como o Azure Container Registry, primeiro tem de marcar a imagem com o nome completo do servidor de início de sessão no registo.

Em primeiro lugar, obtenha o nome completo do servidor de início de sessão do seu registo de contentor do Azure. Execute o seguinte comando [az acr show][az-acr-show] e substitua `<acrName>` pelo nome do registo que acabou de criar:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Por exemplo, se o registo tiver o nome *mycontainerregistry082*:

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
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

Marque a imagem *aci-tutorial-app* com o servidor de login do seu registo de contentores. Além disso, adicione a etiqueta `:v1` ao fim do nome da imagem para indicar o número da versão da imagem. Substitua `<acrLoginServer>` pelo resultado do comando [az acr show][az-acr-show] que executou anteriormente.

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

Agora que marcou a imagem da *aplicação aci-tutorial* com o nome completo do servidor de login do seu registo privado, pode empurrar a imagem para o registo com o comando [de push do estivador.][docker-push] Substitua `<acrLoginServer>` pelo nome completo de servidor de início de sessão que obteve no passo anterior.

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

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
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
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, preparou um registo de contentor do Azure para utilização com o Azure Container Instances e enviou por push uma imagem de contentor para o registo. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Criei um caso de registo de contentores Azure com o Azure CLI
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
