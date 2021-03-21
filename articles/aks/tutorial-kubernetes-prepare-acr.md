---
title: Tutorial do Kubernetes no Azure - Criar um registo de contentor
description: Neste tutorial do Azure Kubernetes Service (AKS), vai criar uma instância do Azure Container Registry e carregar uma imagem de contentor da aplicação de exemplo.
services: container-service
ms.topic: tutorial
ms.date: 01/31/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9f6ec14cea20192aef7d3010201e6613c5d03a9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430968"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Tutorial: Implementar e utilizar o Azure Container Registry

O Registo de Contentores Azure (ACR) é um registo privado para imagens de contentores. Um registo de contentor privado permite-lhe criar e implementar as suas aplicações e código personalizado de forma segura. Neste tutorial, a segunda parte de sete, vai implementar uma instância do ACR e enviar uma imagem de contentor para a mesma. Saiba como:

> [!div class="checklist"]
> * Criar uma instância do Azure Container Registry (ACR)
> * Marcar uma imagem de contentor para o ACR
> * Carregar os ficheiros de imagem para o ACR
> * Ver imagens no registo

Em tutoriais posteriores, este exemplo de ACR é integrado com um cluster Kubernetes em AKS, e uma aplicação é implementada a partir da imagem.

## <a name="before-you-begin"></a>Antes de começar

No [tutorial anterior][aks-tutorial-prepare-app], foi criada uma imagem de contentor para uma aplicação de Votação simples do Azure. Se não tiver criado a imagem de aplicação de Votação do Azure, regresse ao [Tutorial 1 – Criar imagens de contentor][aks-tutorial-prepare-app].

Este tutorial requer que esteja a executar a versão 2.0.53 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry

Para criar um Azure Container Registry, tem de começar por obter um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create][az-group-create]. No exemplo seguinte, é criado um grupo de recursos designado *myResourceGroup* na região *eualeste*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie uma instância do Azure Container Registry com o comando [az acr create][az-acr-create] e indique o seu próprio nome de registo. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. No resto deste tutorial, `<acrName>` é utilizado como um marcador de posição para o nome do registo de contentor. Forneça o seu próprio nome de registo único. O SKU *Básico* é um ponto de entrada com otimização de custos para fins de desenvolvimento que fornece um equilíbrio de armazenamento e débito.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Iniciar sessão no registo de contentor

Para utilizar a instância do ACR, primeiro tem de iniciar sessão. Utilize o comando [az acr login][az-acr-login] e indique o nome exclusivo dado ao registo de contentor no passo anterior.

```azurecli
az acr login --name <acrName>
```

O comando devolve uma mensagem *login concluída* uma vez concluída.

## <a name="tag-a-container-image"></a>Marcar uma imagem de contentor

Para ver uma lista das imagens locais atuais, utilize o comando [docker images][docker-images]:

```console
docker images
```
A saída do comando acima mostra a lista das imagens locais atuais:

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Para utilizar a imagem de contentor *azure-vote-front* com o ACR, a imagem tem de ser marcada com o endereço do servidor de início de sessão do registo. Esta etiqueta é utilizada para encaminhamento ao enviar imagens de contentor para um registo de imagem.

Para obter o endereço do servidor de início de sessão, utilize o comando [az acr list][az-acr-list] e consulte o *loginServer* da seguinte forma:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Agora, marque a sua imagem local *de voto-frontal* com o endereço *acrLoginServer* do registo do contentor. Para indicar a versão da imagem, adicione *:v1* ao fim do nome da imagem:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

Para verificar se as etiquetas são aplicadas, execute novamente [docker images][docker-images].

```console
docker images
```

Uma imagem é marcada com o endereço da instância do ACR e um número de versão.

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
```

## <a name="push-images-to-registry"></a>Enviar imagens para o registo

Com a sua imagem construída e marcada, empurre a imagem *frontal* para o seu exemplo de ACR. Utilize [docker push][docker-push] e indique o seu próprio endereço *acrLoginServer* para o nome da imagem da seguinte forma:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

O envio da imagem para o ACR pode demorar alguns minutos até ser concluído.

## <a name="list-images-in-registry"></a>Listar imagens no registo

Para devolver uma lista de imagens que foram enviadas para a instância do ACR, utilize o comando [az acr repository list][az-acr-repository-list]. Indique o seu próprio `<acrName>` da seguinte forma:

```azurecli
az acr repository list --name <acrName> --output table
```

A saída de exemplo seguinte lista a imagem *azure-vote-front* como disponível no registo:

```output
Result
----------------
azure-vote-front
```

Para ver as etiquetas de uma imagem específica, utilize o comando [az acr repository show-tags][az-acr-repository-show-tags] da seguinte forma:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

A saída de exemplo seguinte mostra a imagem *v1* marcada num passo anterior:

```output
Result
--------
v1
```

Tem agora uma imagem de contentor armazenada numa instância privada do Azure Container Registry. Esta imagem é implementada a partir do ACR num cluster do Kubernetes nos tutoriais seguintes.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um Azure Container Registry e enviou uma imagem para utilização num cluster do AKS. Aprendeu a:

> [!div class="checklist"]
> * Criar uma instância do Azure Container Registry (ACR)
> * Marcar uma imagem de contentor para o ACR
> * Carregar os ficheiros de imagem para o ACR
> * Ver imagens no registo

Avance para o próximo tutorial para saber como implementar um cluster do Kubernetes no Azure.

> [!div class="nextstepaction"]
> [Implementar cluster do Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
