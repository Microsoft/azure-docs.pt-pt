---
title: (DEPRECIADO) Tutorial do Serviço de Contentores Azure - Prepare a ACR
description: Tutorial do Azure Container Service – Prepara ACR
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 087530fd3834c4ec4620c087134bee0ed26bb6c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78399777"
---
# <a name="deprecated-deploy-and-use-azure-container-registry"></a>(DEPRECIADO) Implementar e utilizar o Registo de Contentores Azure

> [!TIP]
> Para a versão atualizada este tutorial que utiliza o Serviço Azure Kubernetes, consulte [Tutorial: Desdobre e utilize](../../aks/tutorial-kubernetes-prepare-acr.md)o Registo de Contentores Azure .

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

O Azure Container Registry (ACR) é um registo privado e baseado no Azure para imagens de contentor do Docker. Este tutorial, parte 2 de 7, orienta-o na implementação de uma instância do Azure Container Registry e no envio de uma imagem de contentor para a mesma. Os passos concluídos incluem:

> [!div class="checklist"]
> * Implementar uma instância do Azure Container Registry (ACR)
> * Marcar uma imagem de contentor para o ACR
> * Carregar os ficheiros de imagem para o ACR

Em tutoriais posteriores, esta instância do ACR é integrada num cluster do Kubernetes do Azure Container Service. 

## <a name="before-you-begin"></a>Antes de começar

No [tutorial anterior](./container-service-tutorial-kubernetes-prepare-app.md), foi criada uma imagem de contentor para uma aplicação de Votação simples do Azure. Se não tiver criado a imagem de aplicação de Votação do Azure, regresse ao [Tutorial 1 – Criar imagens de contentor](./container-service-tutorial-kubernetes-prepare-app.md).

Este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Implementar o Azure Container Registry

Ao implementar um Azure Container Registry, tem de começar por obter um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Neste exemplo, é criado um grupo de recursos chamado `myResourceGroup` na região `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Crie um registo de contentores Azure com o [az acr criar](/cli/azure/acr#az-acr-create) comando. O nome de um Registo de Contentor **tem de ser exclusivo**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Em todo o resto deste tutorial, utilizamos `<acrname>` como um marcador de posição para o nome do registo de contentor.

## <a name="container-registry-login"></a>Iniciar sessão no registo de contentor

Utilize o comando [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) para iniciar sessão na instância do ACR. Tem de fornecer o nome exclusivo dado ao registo de contentor quando este foi criado.

```azurecli
az acr login --name <acrName>
```

O comando devolve uma mensagem "Início de sessão com êxito" depois de concluir.

## <a name="tag-container-images"></a>Marcar imagens de contentor

Para ver uma lista das imagens atuais, utilize o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Saída:

```output
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Cada imagem de contentor tem de ser marcada com o nome loginServer do registo. Esta etiqueta é utilizada para encaminhamento ao enviar imagens de contentor para um registo de imagem.

Para obter o nome loginServer, execute o seguinte comando.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Agora, marque a imagem `azure-vote-front` com o loginServer do registo de contentor. Além disso, adicione `:v1` ao final do nome da imagem. Esta etiqueta identifica a versão da imagem.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Depois de marcada, execute [imagens do docker](https://docs.docker.com/engine/reference/commandline/images/) para verificar a operação.

```bash
docker images
```

Saída:

```output
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Enviar imagens para o registo

Envie a imagem `azure-vote-front` para o registo. 

Utilizando o seguinte exemplo, substitua o nome loginServer do ACR pelo loginServer do seu ambiente.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Este processo demora poucos minutos a concluir.

## <a name="list-images-in-registry"></a>Listar imagens no registo

Para devolver uma lista de imagens que foram enviadas para o seu Azure Container Registry, utilize o comando [az acr repository list](/cli/azure/acr/repository#az-acr-repository-list). Atualize o comando com o nome de instância do ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```output
Result
----------------
azure-vote-front
```

Em seguida, para ver as etiquetas de uma imagem específica, utilize o comando [az acr repository show-tags](/cli/azure/acr/repository).

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Saída:

```output
Result
--------
v1
```

Ao concluir o tutorial, a imagem de contentor foi armazenada numa instância privada do Azure Container Registry. Esta imagem é implementada do ACR para um cluster do Kubernetes em tutoriais posteriores.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, um Azure Container Registry foi preparado para utilização num cluster do AKS Kubernetes. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Implementou uma instância do Azure Container Registry
> * Marcou uma imagem de contentor para o ACR
> * Carregou a imagem para o ACR

Avance para o próximo tutorial para saber mais sobre implementar um cluster do Kubernetes no Azure.

> [!div class="nextstepaction"]
> [Implementar cluster do Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)
