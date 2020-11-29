---
title: Atualizar imagens do nó do serviço Azure Kubernetes (AKS)
description: Saiba como atualizar as imagens nos nós de cluster AKS e nas piscinas de nó.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: e8214345bd1c328f0996f8aa8a2a8bb402a76e8d
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309601"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Atualização de imagem do nó de Azure Kubernetes (AKS)

A AKS suporta a atualização das imagens num nó para que esteja atualizado com as mais recentes atualizações de sistemas operativos e de tempo de execução. A AKS fornece uma nova imagem por semana com as mais recentes atualizações, pelo que é benéfico atualizar regularmente as imagens do seu nó para as funcionalidades mais recentes, incluindo patches Linux ou Windows. Este artigo mostra-lhe como atualizar as imagens do nó de cluster AKS e como atualizar as imagens do node pool sem atualizar a versão de Kubernetes.

Para obter mais informações sobre as imagens mais recentes fornecidas pela AKS, consulte as notas de lançamento da [AKS](https://github.com/Azure/AKS/releases).

Para obter informações sobre a atualização da versão Kubernetes para o seu cluster, consulte [atualizar um cluster AKS][upgrade-cluster].

> [!NOTE]
> O cluster AKS deve utilizar conjuntos de balança de máquinas virtuais para os nós.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Verifique se a piscina do nó está na última imagem do nó

Pode ver qual é a versão mais recente da imagem do nó disponível para a sua piscina de nó com o seguinte comando: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

Na saída pode ver o `latestNodeImageVersion` mesmo no exemplo abaixo:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

Assim, para `nodepool1` a última imagem do nó disponível é `AKSUbuntu-1604-2020.10.28` . Pode agora compará-lo com a versão atual da imagem do nó em uso pelo seu conjunto de nó saindo:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Um exemplo de saída seria:

```output
"AKSUbuntu-1604-2020.10.08"
```

Assim, neste exemplo, pode atualizar da versão de imagem atual `AKSUbuntu-1604-2020.10.08` para a versão mais recente `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Atualizar todos os nós em todas as piscinas de nós

A atualização da imagem do nó é feita com `az aks upgrade` . Para atualizar a imagem do nó, utilize o seguinte comando:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Durante a atualização, verifique o estado das imagens do nó com o seguinte `kubectl` comando para obter as etiquetas e filtrar as informações de imagem do nó atual:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Quando a atualização estiver concluída, utilize `az aks show` para obter os detalhes atualizados do conjunto de nós. A imagem atual do nó é mostrada na `nodeImageVersion` propriedade.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Atualize uma piscina de nó específico

A atualização da imagem numa piscina de nós é semelhante à melhoria da imagem num cluster.

Para atualizar a imagem de SO do conjunto de nó sem fazer uma atualização do cluster Kubernetes, utilize a `--node-image-only` opção no seguinte exemplo:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Durante a atualização, verifique o estado das imagens do nó com o seguinte `kubectl` comando para obter as etiquetas e filtrar as informações de imagem do nó atual:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Quando a atualização estiver concluída, utilize `az aks nodepool show` para obter os detalhes atualizados do conjunto de nós. A imagem atual do nó é mostrada na `nodeImageVersion` propriedade.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Atualizar imagens de nó com aumento de nó

Para acelerar o processo de atualização da imagem do nó, pode atualizar as imagens do nó utilizando um valor personalizável de aumento de nó. Por predefinição, a AKS usa um nó adicional para configurar upgrades.

Se quiser aumentar a velocidade das atualizações, use o `--max-surge` valor para configurar o número de nós a utilizar para upgrades para que se completem mais rapidamente. Para saber mais sobre as compensações de `--max-surge` várias configurações, consulte a atualização do [número de nóises personalizados][max-surge].

O seguinte comando define o valor máximo de aumento para a realização de uma atualização de imagem de nó:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Durante a atualização, verifique o estado das imagens do nó com o seguinte `kubectl` comando para obter as etiquetas e filtrar as informações de imagem do nó atual:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Use `az aks nodepool show` para obter os detalhes atualizados da piscina do nó. A imagem atual do nó é mostrada na `nodeImageVersion` propriedade.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Próximos passos

- Consulte as notas de lançamento da [AKS](https://github.com/Azure/AKS/releases) para obter informações sobre as imagens mais recentes do nó.
- Saiba como atualizar a versão Kubernetes com [upgrade de um cluster AKS.][upgrade-cluster]
- [Aplicar atualizações de segurança e kernel aos nós Linux no Serviço Azure Kubernetes (AKS)][security-update]
- Saiba mais sobre várias piscinas de nós e como atualizar piscinas de nó com [Criar e gerir várias piscinas de nó.][use-multiple-node-pools]

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
