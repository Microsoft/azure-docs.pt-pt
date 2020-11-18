---
title: Atualizar imagens do nó do serviço Azure Kubernetes (AKS)
description: Saiba como atualizar as imagens nos nós de cluster AKS e nas piscinas de nó.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 211190228c1ea9c98004b55da96ad38808821d67
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682388"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Atualização de imagem do nó de Azure Kubernetes (AKS)

A AKS suporta a atualização das imagens num nó para que esteja atualizado com as mais recentes atualizações de sistemas operativos e de tempo de execução. A AKS fornece uma nova imagem por semana com as mais recentes atualizações, pelo que é benéfico atualizar regularmente as imagens do seu nó para as funcionalidades mais recentes, incluindo patches Linux ou Windows. Este artigo mostra-lhe como atualizar as imagens do nó de cluster AKS, bem como como atualizar as imagens do node pool sem atualizar a versão de Kubernetes.

Se estiver interessado em saber mais sobre as imagens mais recentes fornecidas pela AKS, consulte as notas de lançamento da [AKS](https://github.com/Azure/AKS/releases) para mais detalhes.

Para obter informações sobre a atualização da versão Kubernetes para o seu cluster, consulte [atualizar um cluster AKS][upgrade-cluster].

## <a name="limitations"></a>Limitações

* O cluster AKS deve utilizar conjuntos de balança de máquinas virtuais para os nós.

## <a name="install-the-aks-cli-extension"></a>Instale a extensão AKS CLI

Antes da próxima versão core CLI ser lançada, precisa da extensão CLI *de pré-visualização aks* para utilizar o upgrade de imagem do nó. Utilize o comando [de adicionar extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

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

Para atualizar a imagem de SO do conjunto de nó sem realizar uma atualização do cluster Kubernetes, utilize a `--node-image-only` opção no seguinte exemplo:

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
