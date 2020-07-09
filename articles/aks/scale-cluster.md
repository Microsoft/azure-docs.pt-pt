---
title: Dimensionar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como escalar o número de nós num cluster Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79368422"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Dimensionar uma contagem de nós num cluster do Azure Kubernetes Service (AKS)

Se as necessidades de recursos das suas aplicações mudarem, pode escalar manualmente um cluster AKS para executar um número diferente de nós. Quando se reduz, os nós são cuidadosamente [isolados e drenados][kubernetes-drain] para minimizar a perturbação das aplicações de funcionamento. Quando se escala, a AKS espera até que os nós sejam marcados `Ready` pelo cluster Kubernetes antes de serem programados os pods.

## <a name="scale-the-cluster-nodes"></a>Dimensionar nós de cluster

Primeiro, obtenha o *nome* da sua piscina de nó usando o comando [de show az aks.][az-aks-show] O exemplo a seguir obtém o nome do número de números para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

A saída de exemplo a seguir mostra que o *nome* é *nodepool1*:

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

Utilize o comando [de escala az aks][az-aks-scale] para escalar os nós de cluster. O exemplo a seguir escala um cluster chamado *myAKSCluster* para um único nó. Forneça o seu próprio *nome de nodepool* do comando anterior, como *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

A saída de exemplo a seguir mostra que o cluster escalou com sucesso para um nó, como mostrado na secção *agentPoolProfiles:*

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

## <a name="next-steps"></a>Próximos passos

Neste artigo, escalou manualmente um cluster AKS para aumentar ou diminuir o número de nós. Também pode utilizar o [autoescalador][cluster-autoscaler] de cluster para escalar automaticamente o seu cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
