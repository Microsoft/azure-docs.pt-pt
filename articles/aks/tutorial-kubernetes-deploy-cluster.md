---
title: Tutorial do Kubernetes no Azure - Implementar um cluster
description: Neste tutorial do Azure Kubernetes Service (AKS), irá criar um cluster do AKS e utilizar o kubectl para ligar ao nó principal do Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 8ad542a3614253e11331e9b49513a887aff65890
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512925"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: Implementar um cluster do Serviço Kubernetes do Azure (AKS)

O Kubernetes fornece uma plataforma distribuída para aplicações em contentores. Com o AKS, você pode criar rapidamente um cluster kubernetes pronto para produção. Neste tutorial, parte três de sete, é implementado um cluster do Kubernetes no AKS. Saiba como:

> [!div class="checklist"]
> * Implantar um cluster kubernetes AKS que possa se autenticar em um registro de contêiner do Azure
> * Instalar a CLI do Kubernetes (kubectl)
> * Configurar o kubectl para ligar ao seu cluster do AKS

Em Tutoriais adicionais, o aplicativo de voto do Azure é implantado no cluster, dimensionado e atualizado.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi criada e carregada uma imagem de contentor para uma instância do Azure Container Registry. Se você ainda não realizou essas etapas e gostaria de acompanhar, comece no [tutorial 1 – criar imagens de contêiner][aks-tutorial-prepare-app].

Este tutorial requer que você esteja executando o CLI do Azure versão 2.0.75 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster do Kubernetes

Os clusters do AKS podem utilizar os controlos de acesso baseado em funções (RBAC) do Kubernetes. Estes controlos permitem-lhe definir o acesso a recursos com base nas funções atribuídas a utilizadores. As permissões são combinadas se um usuário recebe várias funções e as permissões podem ser delimitadas para um único namespace ou em todo o cluster. Por predefinição, a CLI do Azure ativa automaticamente o RBAC quando cria um cluster do AKS.

Crie um cluster do AKS com [az aks create][]. O exemplo seguinte cria um cluster com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*. Este grupo de recursos foi criado no [tutorial anterior][aks-tutorial-prepare-acr]. Para permitir que um cluster AKS interaja com outros recursos do Azure, uma entidade de serviço Azure Active Directory é criada automaticamente, já que você não especificou um. Aqui, essa entidade de serviço [recebe o direito de efetuar pull de imagens][container-registry-integration] da instância do ACR (registro de contêiner do Azure) que você criou no tutorial anterior.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Depois de alguns minutos, a implantação é concluída e retorna informações formatadas em JSON sobre a implantação do AKS.

> [!NOTE]
> Para garantir que o cluster opere de forma confiável, você deve executar pelo menos 2 (dois) nós.

## <a name="install-the-kubernetes-cli"></a>Instalar a CLI do Kubernetes

Para se conectar ao cluster kubernetes do computador local, use [kubectl][kubectl], o cliente de linha de comando kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Ligar ao cluster com o kubectl

Para configurar `kubectl` para se conectar ao cluster kubernetes, use o comando [AZ AKs Get-Credentials][] . O exemplo a seguir obtém as credenciais para o cluster AKS chamado *myAKSCluster* no grupo *MyResource*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão com o cluster, execute o comando [kubectl Get Nodes][kubectl-get] :

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, um cluster do cluster do Kubernetes foi implementado no AKS e configurou `kubectl` para se ligar ao mesmo. Aprendeu a:

> [!div class="checklist"]
> * Implantar um cluster kubernetes AKS que possa se autenticar em um registro de contêiner do Azure
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
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
