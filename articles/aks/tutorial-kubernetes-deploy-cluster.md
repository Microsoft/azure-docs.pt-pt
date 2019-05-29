---
title: Tutorial do Kubernetes no Azure - Implementar um cluster
description: Neste tutorial do Azure Kubernetes Service (AKS), irá criar um cluster do AKS e utilizar o kubectl para ligar ao nó principal do Kubernetes.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 020b5935595506732c1c1425179741c45f8326d7
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304459"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: Implementar um cluster do Azure Kubernetes Service (AKS)

O Kubernetes dispõe de uma plataforma distribuída para aplicações em contentores. Com o AKS, pode criar rapidamente um cluster de Kubernetes pronto para produção. Neste tutorial, parte três de sete, é implementado um cluster do Kubernetes no AKS. Saiba como:

> [!div class="checklist"]
> * Criar um principal de serviço para interações de recursos
> * Implementar um cluster do Kubernetes AKS
> * Instalar a CLI do Kubernetes (kubectl)
> * Configurar o kubectl para ligar ao seu cluster do AKS

Nos tutoriais adicionais, a aplicação Azure Vote é implementada no cluster, dimensionada e atualizada.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi criada e carregada uma imagem de contentor para uma instância do Azure Container Registry. Se ainda não concluiu estes passos e pretende acompanhar, inicie na [Tutorial 1 – criar imagens de contentor][aks-tutorial-prepare-app].

Este tutorial requer que está a executar a CLI do Azure versão 2.0.53 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. Este principal de serviço pode ser criado automaticamente com a CLI do Azure ou o portal, ou pode criar previamente um e atribuir permissões adicionais. Neste tutorial, vai criar um principal de serviço, conceder acesso à instância do Azure Container Registry (ACR) criado no tutorial anterior e, em seguida, criar um cluster do AKS.

Crie um principal de serviço com o comando [az ad sp create-for-rbac][]. O parâmetro `--skip-assignment` limita a atribuição de permissões adicionais. Por predefinição, este principal de serviço é válido durante um ano.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

O resultado é semelhante ao seguinte exemplo:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anote o *appId* e a *palavra-passe*. São utilizados os seguintes valores nos passos seguintes.

## <a name="configure-acr-authentication"></a>Configurar a autenticação do ACR

Para aceder a imagens armazenadas no ACR, tem de conceder os direitos corretos ao principal de serviço do AKS para solicitar imagens do ACR.

Primeiro, obtenha o ID do recurso do ACR com [az acr show][]. Atualize o nome do registo `<acrName>` para o nome da sua instância do ACR e o grupo de recursos onde está localizada a instância do ACR.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Para conceder o acesso correto para o cluster do AKS para imagens de pull armazenadas no ACR, atribuir os `AcrPull` função com o [criação da atribuição de função de az][] comando. Substitua `<appId`> e `<acrId>` pelos valores recolhidos nos dois passos anteriores.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster do Kubernetes

Os clusters do AKS podem utilizar os controlos de acesso baseado em funções (RBAC) do Kubernetes. Estes controlos permitem-lhe definir o acesso a recursos com base nas funções atribuídas a utilizadores. As permissões são combinadas se um utilizador é atribuído a várias funções e permissões podem ser confinadas para um único namespace ou entre o cluster inteiro. Por predefinição, a CLI do Azure ativa automaticamente o RBAC quando cria um cluster do AKS.

Crie um cluster do AKS com [az aks create][]. O exemplo seguinte cria um cluster com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*. Este grupo de recursos foi criado no [tutorial anterior][aks-tutorial-prepare-acr]. Indique o seu próprio `<appId>` e `<password>` do passo anterior, onde criou o principal de serviço.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Após alguns minutos, a implementação estiver concluída e devolve o formato JSON para obter informações sobre a implementação do AKS.

## <a name="install-the-kubernetes-cli"></a>Instalar a CLI do Kubernetes

Para ligar ao cluster de Kubernetes a partir do computador local, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Ligar ao cluster com o kubectl

Para configurar `kubectl` para ligar ao seu cluster do Kubernetes, utilize o [az aks get-credentials][] comando. O exemplo seguinte obtém credenciais para o cluster do AKS com o nome *myAKSCluster* no *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, execute o comando [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, um cluster do cluster do Kubernetes foi implementado no AKS e configurou `kubectl` para se ligar ao mesmo. Aprendeu a:

> [!div class="checklist"]
> * Criar um principal de serviço para interações de recursos
> * Implementar um cluster do Kubernetes AKS
> * Instalar a CLI do Kubernetes (kubectl)
> * Configurar o kubectl para ligar ao seu cluster do AKS

Prossiga para o próximo tutorial para saber como implementar uma aplicação no cluster.

> [!div class="nextstepaction"]
> [Implementar aplicação no Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[criação da atribuição de função de az]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
