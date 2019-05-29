---
title: Tutorial do Kubernetes no Azure - Atualizar cluster para versão superior
description: Neste tutorial do Azure Kubernetes Service (AKS), saiba como atualizar um cluster do AKS existente para a versão do Kubernetes mais recente disponível.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 09e3fab67486b4677c5848d81fb39a68e511b6ac
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305216"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Tutorial: Atualizar o Kubernetes no Azure Kubernetes Service (AKS)

Como parte da aplicação e do ciclo de vida do cluster, pode querer atualizar para a versão mais recente disponível do Kubernetes e utilizar novas funcionalidades. Um cluster do Serviço Kubernetes do Azure (AKS) poder ser atualizado através da CLI do Azure.

Neste tutorial, parte sete de sete, é atualizado um cluster do Kubernetes. Saiba como:

> [!div class="checklist"]
> * Identificar versões do Kubernetes atuais e disponíveis
> * Atualizar os nós do Kubernetes
> * Validar uma atualização com êxito

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi compactada uma aplicação numa imagem de contentor. A imagem foi carregada para o Azure Container Registry e criado um cluster do AKS. A aplicação foi implementada, em seguida, para o cluster do AKS. Se ainda não concluiu estes passos e pretende acompanhar, inicie com [Tutorial 1 – criar imagens de contentor][aks-tutorial-prepare-app].

Este tutorial requer a execução da versão 2.0.53 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Obter versões de cluster disponíveis

Antes de atualizar um cluster, utilize o comando [az aks get-upgrades][] para verificar que versões do Kubernetes estão disponíveis para atualização:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

No exemplo seguinte, a versão atual é *1.9.11*, e as versões disponíveis são apresentadas sob a *atualizações* coluna.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.9.11           1.9.11             1.10.8, 1.10.9
```

## <a name="upgrade-a-cluster"></a>Atualizar um cluster

Para minimizar a interrupção aplicações em execução, nós do AKS são cuidadosamente isolados e drenados. Nesse processo, são realizadas as seguintes etapas:

1. O agendador de Kubernetes impede que os pods adicionais a ser agendados num nó que está a ser atualizado.
1. Pods em execução no nó são agendadas nos outros nós do cluster.
1. É criado um nó que executa os componentes mais recentes do Kubernetes.
1. Quando o novo nó está pronta e associado ao cluster, o agendador de Kubernetes começa a execução de pods na mesma.
1. O nó antigo é eliminado e o processo de cordon e drenagem de começa o próximo nó do cluster.

Utilize o comando [az aks upgrade][] para atualizar o cluster do AKS. O exemplo seguinte atualiza o cluster para a versão do Kubernetes *1.10.9*.

> [!NOTE]
> Pode atualizar apenas uma versão secundária de cada vez. Por exemplo, pode atualizar a partir *1.9.11* ao *1.10.9*, mas não é possível atualizar a partir *1.9.6* para *1.11.x* diretamente. Para atualizar a partir *1.9.11* ao *1.11.x*, primeiro atualizar do *1.9.11* para *1.10.x*, em seguida, executar outra atualização do *1.10.x* para *1.11.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.9
```

O seguinte exemplo condensado saída mostra o *kubernetesVersion* agora relatórios *1.10.9*:

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
  "kubernetesVersion": "1.10.9",
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

O resultado de exemplo seguinte mostra a executa o cluster do AKS *KubernetesVersion 1.10.9*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.9               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Eliminar o cluster

Como este tutorial é a última parte da série, poderá eliminar o cluster do AKS. Uma vez que os nós do Kubernetes são executados em máquinas virtuais (VMs) do Azure, continuam a incorrer em custos, mesmo que não utilize o cluster. Utilize o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete].

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, atualizou o Kubernetes num cluster do AKS. Aprendeu a:

> [!div class="checklist"]
> * Identificar versões do Kubernetes atuais e disponíveis
> * Atualizar os nós do Kubernetes
> * Validar uma atualização com êxito

Siga esta ligação para saber mais sobre o AKS.

> [!div class="nextstepaction"]
> [Descrição geral do AKS][aks-intro]

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
