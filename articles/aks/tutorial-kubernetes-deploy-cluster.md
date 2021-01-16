---
title: Tutorial do Kubernetes no Azure - Implementar um cluster
description: Neste tutorial do Azure Kubernetes Service (AKS), irá criar um cluster do AKS e utilizar o kubectl para ligar ao nó principal do Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a8e0ddcd77c26a00cf784fb8c2372734314dc0bb
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250643"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: Implementar um cluster do Serviço Kubernetes do Azure (AKS)

O Kubernetes dispõe de uma plataforma distribuída para aplicações em contentores. Com a AKS, pode criar rapidamente um cluster Kubernetes pronto para a produção. Neste tutorial, parte três de sete, é implementado um cluster do Kubernetes no AKS. Saiba como:

> [!div class="checklist"]
> * Implementar um cluster AKS kubernetes que pode autenticar para um registo de contentores Azure
> * Instalar a CLI do Kubernetes (kubectl)
> * Configurar o kubectl para ligar ao seu cluster do AKS

Em tutoriais posteriores, a aplicação Azure Vote é implantada no cluster, dimensionada e atualizada.

## <a name="before-you-begin"></a>Before you begin

Nos tutoriais anteriores, foi criada e carregada uma imagem de contentor para uma instância do Azure Container Registry. Se não fez estes passos e gostaria de seguir em frente, comece no [Tutorial 1 – Crie imagens de contentores.][aks-tutorial-prepare-app]

Este tutorial requer que esteja a executar a versão 2.0.53 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster do Kubernetes

Os clusters AKS podem usar o controlo de acesso baseado em funções de Kubernetes (Kubernetes RBAC). Estes controlos permitem-lhe definir o acesso a recursos com base nas funções atribuídas a utilizadores. As permissões são combinadas se um utilizador tiver várias funções, e as permissões podem ser miradas para um único espaço de nome ou em todo o cluster. Por padrão, o Azure CLI ativa automaticamente o RBAC de Kubernetes quando cria um cluster AKS.

Crie um cluster do AKS com [az aks create][]. O exemplo seguinte cria um cluster com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*. Este grupo de recursos foi criado no [tutorial anterior][aks-tutorial-prepare-acr] na região *leste.* O exemplo a seguir não especifica uma região, pelo que o cluster AKS também é criado na região *leste.* Para obter mais informações, consulte [Quotas, restrições de tamanho de máquina virtual e disponibilidade de região no Serviço Azure Kubernetes (AKS)][quotas-skus-regions] para obter mais informações sobre os limites de recursos e disponibilidade da região para AKS.

Para permitir que um cluster AKS interaja com outros recursos Azure, é criado automaticamente um diretor de serviço azure Ative, uma vez que não especificou um. Aqui, este diretor de serviço tem o direito de [extrair imagens][container-registry-integration] do caso Azure Container Registry (ACR) que criou no tutorial anterior. Para executar o comando com sucesso, é-lhe exigido que tenha uma função de administrador de conta **Proprietário** ou **Azure** na subscrição do Azure.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Para evitar a necessidade de uma função de administrador de conta **Proprietário** ou **Azure,** também pode configurar manualmente um diretor de serviço para extrair imagens da ACR. Para mais informações, consulte [a autenticação ACR com os principais serviços](../container-registry/container-registry-auth-service-principal.md) ou [Autenticar de Kubernetes com um segredo de puxar](../container-registry/container-registry-auth-kubernetes.md). Em alternativa, pode utilizar uma [identidade gerida](use-managed-identity.md) em vez de um diretor de serviço para uma gestão mais fácil.

Após alguns minutos, a implementação completa e devolve informações formatadas com JSON sobre a implementação AKS.

> [!NOTE]
> Para garantir que o seu cluster funcione de forma fiável, deverá executar pelo menos 2 (dois) nós.

## <a name="install-the-kubernetes-cli"></a>Instalar a CLI do Kubernetes

Para ligar ao cluster de Kubernetes a partir do computador local, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Ligar ao cluster com o kubectl

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][]. O exemplo a seguir obtém credenciais para o cluster AKS chamado *myAKSCluster* no *myResourceGroup:*

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao seu cluster, executar o comando [kubectl get nodes][kubectl-get] para devolver uma lista dos nóns do cluster:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, um cluster do cluster do Kubernetes foi implementado no AKS e configurou `kubectl` para se ligar ao mesmo. Aprendeu a:

> [!div class="checklist"]
> * Implementar um cluster AKS kubernetes que pode autenticar para um registo de contentores Azure
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
[quotas-skus-regions]: quotas-skus-regions.md
