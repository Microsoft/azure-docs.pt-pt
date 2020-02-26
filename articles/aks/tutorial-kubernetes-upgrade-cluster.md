---
title: Tutorial do Kubernetes no Azure - Atualizar cluster para versão superior
description: Neste tutorial do Azure Kubernetes Service (AKS), saiba como atualizar um cluster do AKS existente para a versão do Kubernetes mais recente disponível.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: 57cad9472c599bf0ad8f3e3d2ff53cb224db689a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593133"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Tutorial: Atualizar o Kubernetes no Serviço Kubernetes do Azure (AKS)

Como parte da aplicação e do ciclo de vida do cluster, pode querer atualizar para a versão mais recente disponível do Kubernetes e utilizar novas funcionalidades. Um cluster do Serviço Kubernetes do Azure (AKS) poder ser atualizado através da CLI do Azure.

Neste tutorial, parte sete de sete, é atualizado um cluster do Kubernetes. Saiba como:

> [!div class="checklist"]
> * Identificar versões do Kubernetes atuais e disponíveis
> * Atualizar os nós do Kubernetes
> * Validar uma atualização com êxito

## <a name="before-you-begin"></a>Antes de começar

Em tutoriais anteriores, uma aplicação foi embalada numa imagem de contentor. Esta imagem foi enviada para o Registo de Contentores Azure, e você criou um cluster AKS. A aplicação foi então implantada para o cluster AKS. Se não tiver feito estes passos, e gostaria de seguir em frente, comece com tutorial 1 – Criar imagens de [contentores.][aks-tutorial-prepare-app]

Este tutorial requer que esteja a executar a versão Azure CLI 2.0.53 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Obter versões de cluster disponíveis

Antes de atualizar um cluster, utilize o comando [az aks get-upgrades][] para verificar que versões do Kubernetes estão disponíveis para atualização:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

No exemplo seguinte, a versão atual é *de 1.13.10*, e as versões disponíveis são mostradas na coluna *Upgrades.*

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.13.10          1.13.10            1.14.5, 1.14.6
```

## <a name="upgrade-a-cluster"></a>Atualizar um cluster

Para minimizar a perturbação das aplicações em execução, os nós AKS são cuidadosamente isolados e drenados. Neste processo, são realizados os seguintes passos:

1. O programador Kubernetes evita que as cápsulas adicionais sejam programadas num nó que deve ser atualizado.
1. As cápsulas de funcionamento no nó estão programadas em outros nós do cluster.
1. É criado um nó que executa os mais recentes componentes kubernetes.
1. Quando o novo nó está pronto e se juntou ao cluster, o programador kubernetes começa a executar cápsulas nele.
1. O nó antigo é apagado, e o próximo nó no cluster inicia o processo de cordão e drenagem.

Utilize o comando [az aks upgrade][] para atualizar o cluster do AKS. O exemplo seguinte atualiza o cluster para a versão Kubernetes *1.14.6*.

> [!NOTE]
> Pode atualizar apenas uma versão secundária de cada vez. Por exemplo, pode fazer upgrade de *1.12.x* para *1.13.x,* mas não pode fazer upgrade de *1.12.x* para *1.14.x* diretamente. Para atualizar de *1.12.x* para *1.14.x*, primeiro upgrade de *1.12.x* para *1.13.x,* em seguida, execute outra atualização de *1.13.x* para *1.14.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.14.6
```

A saída de exemplo condensado seguinte mostra que os *kubernetesVersion* reportam agora *1.14.6:*

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.14.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Validar uma atualização

Confirme se a atualização foi concluída com êxito com o comando [az aks show][], da seguinte forma:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo seguinte mostra que o cluster AKS executa *kubernetesVersion 1.14.6*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.14.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Eliminar o cluster

Como este tutorial é a última parte da série, você pode querer apagar o cluster AKS. Uma vez que os nós do Kubernetes são executados em máquinas virtuais (VMs) do Azure, continuam a incorrer em custos, mesmo que não utilize o cluster. Utilize o [grupo AZ eliminar][az-group-delete] o comando para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter medidas sobre como remover o diretor de serviço, consulte as [principais considerações e a eliminação do serviço AKS.][sp-delete]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atualizou o Kubernetes num cluster do AKS. Aprendeu a:

> [!div class="checklist"]
> * Identificar versões do Kubernetes atuais e disponíveis
> * Atualizar os nós do Kubernetes
> * Validar uma atualização com êxito

Siga esta ligação para saber mais sobre o AKS.

> [!div class="nextstepaction"]
> [Visão geral do AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
