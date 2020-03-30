---
title: Dimensionar um cluster do Azure Kubernetes Service (AKS)
description: Aprenda a escalar o número de nós num cluster azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368422"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Dimensionar uma contagem de nós num cluster do Azure Kubernetes Service (AKS)

Se as necessidades de recursos das suas aplicações mudarem, pode escalar manualmente um cluster AKS para executar um número diferente de nós. Quando se reduz a escala, os nós são cuidadosamente [isolados e drenados][kubernetes-drain] para minimizar as perturbações nas aplicações de funcionamento. Quando se escala, o AKS espera até `Ready` que os nós sejam marcados pelo aglomerado kubernetes antes de as cápsulas serem programadas.

## <a name="scale-the-cluster-nodes"></a>Dimensionar nós de cluster

Primeiro, pegue o *nome* da sua piscina de nó usando o comando [do az aks show.][az-aks-show] O exemplo seguinte obtém o nome da piscina do nó para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

A saída de exemplo a seguir mostra que o *nome* é *nodepool1:*

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Use o comando de [escala az aks][az-aks-scale] para escalar os nós do cluster. O exemplo seguinte escala um cluster chamado *myAKSCluster* para um único nó. Forneça o seu próprio nome de *nodepool* do comando anterior, como *nodepool1:*

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

A saída de exemplo seguinte mostra que o cluster tem escalado com sucesso para um nó, como mostrado na secção de Perfis de Dados do *agente:*

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, escalou manualmente um cluster AKS para aumentar ou diminuir o número de nós. Também pode utilizar o [autodimensionador][cluster-autoscaler] do cluster para escalar automaticamente o seu cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
