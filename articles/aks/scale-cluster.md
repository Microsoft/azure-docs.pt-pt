---
title: Dimensionar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como dimensionar o número de nós num cluster do Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfoulds
ms.openlocfilehash: de3f8613c93715aecf7e9e066a8ad1d82e4379e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475127"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Dimensionar a contagem de nós num cluster do Azure Kubernetes Service (AKS)

Se o recurso tem de alterar seus aplicativos, pode dimensionar manualmente um cluster do AKS para executar um número diferente de nós. Quando reduzir verticalmente, os nós são cuidadosamente [isolados e drenados] [ kubernetes-drain] para minimizar a interrupção de aplicações em execução. Ao aumentar verticalmente as, AKS aguarda até que nós são marcados `Ready` pelo cluster de Kubernetes, antes de pods são agendadas nos mesmos.

## <a name="scale-the-cluster-nodes"></a>Dimensionar nós de cluster

Primeiro, obtenha o *name* do seu conjunto de nó com o [show do az aks] [ az-aks-show] comando. O exemplo seguinte obtém o nome do conjunto de nó para o cluster com o nome *myAKSCluster* no *myResourceGroup* grupo de recursos:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

O resultado de exemplo seguinte mostra que o *name* é *nodepool1*:

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

Utilize o [dimensionamento do az aks] [ az-aks-scale] comando para dimensionar os nós de cluster. O exemplo seguinte dimensiona um cluster com o nome *myAKSCluster* para um único nó. Fornecer seu próprio *– nodepool-name* do comando anterior, tal como *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

O resultado de exemplo seguinte mostra o cluster foi dimensionada com êxito para um nó, como mostra a *agentPoolProfiles* secção:

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

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, dimensionados manualmente um cluster do AKS para aumentar ou diminuir o número de nós. Também pode utilizar o [dimensionamento automático de clusters] [ cluster-autoscaler] (atualmente em pré-visualização no AKS) para dimensionar automaticamente o seu cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
