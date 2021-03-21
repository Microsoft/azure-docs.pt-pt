---
title: Dimensionar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como escalar o número de nós num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: fdb61bf090351894329c24eb1a3c73d627e622e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173771"
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

Utilize o comando [de escala az aks][az-aks-scale] para escalar os nós de cluster. O exemplo a seguir escala um cluster chamado *myAKSCluster* para um único nó. Forneça o seu próprio `--nodepool-name` comando a partir do comando anterior, como *nodepool1*:

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


## <a name="scale-user-node-pools-to-0"></a>Piscinas `User` de nó de escala para 0

Ao contrário `System` das piscinas de nó que requerem sempre nós de corrida, `User` as piscinas de nó permitem-lhe escalar até 0. Para saber mais sobre as diferenças entre o sistema e as piscinas de nó de utilizador, consulte [o Sistema e as piscinas de nó de utilizador.](use-system-pools.md)

Para escalar um pool de utilizador para 0, pode utilizar a [escala de nodepool az aks][az-aks-nodepool-scale] em alternativa ao `az aks scale` comando acima, e definir 0 como contagem de nós.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

Também pode autoescalar `User` piscinas de nó para 0 nós, definindo o `--min-count` parâmetro do Cluster [Autoscaler](cluster-autoscaler.md) para 0.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, escalou manualmente um cluster AKS para aumentar ou diminuir o número de nós. Também pode utilizar o [autoescalador][cluster-autoscaler] de cluster para escalar automaticamente o seu cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale