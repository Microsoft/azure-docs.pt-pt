---
title: Dimensionar um cluster do AKS (serviço kubernetes do Azure)
description: Saiba como dimensionar o número de nós em um cluster do AKS (serviço kubernetes do Azure).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 719f45aeeb5c7aa7e9b5e597ed461808c9d2b005
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472597"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Dimensionar a contagem de nós em um cluster do serviço de kubernetes do Azure (AKS)

Se as necessidades de recursos de seus aplicativos forem alteradas, você poderá dimensionar manualmente um cluster AKS para executar um número diferente de nós. Ao reduzir verticalmente, os nós são cuidadosamente [isoladosdos e drenados][kubernetes-drain] para minimizar a interrupção na execução de aplicativos. Quando você escala verticalmente, o AKS aguarda até que os nós sejam marcados `Ready` pelo cluster kubernetes antes de os pods serem agendados neles.

## <a name="scale-the-cluster-nodes"></a>Dimensionar nós de cluster

Primeiro, obtenha o *nome* do pool de nós usando o comando [AZ AKs show][az-aks-show] . O exemplo a seguir obtém o nome do pool de nós para o cluster chamado *myAKSCluster* no grupo de recursos *MyResource* Group:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

A saída de exemplo a seguir mostra que o *nome* é *nodepool1*:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

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

Use o comando [AZ AKs Scale][az-aks-scale] para dimensionar os nós de cluster. O exemplo a seguir dimensiona um cluster chamado *myAKSCluster* para um único nó. Forneça seu próprio *--nodepool* do comando anterior, como *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

A saída de exemplo a seguir mostra que o cluster foi dimensionado com êxito para um nó, conforme mostrado na seção *agentPoolProfiles* :

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

Neste artigo, você dimensionou manualmente um cluster AKS para aumentar ou diminuir o número de nós. Você também pode usar o [dimensionamento][cluster-autoscaler] automático do cluster para dimensionar automaticamente o cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
