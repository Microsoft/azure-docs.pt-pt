---
title: Atualizar imagens do nó do serviço Azure Kubernetes (AKS)
description: Saiba como atualizar as imagens nos nós de cluster AKS e nas piscinas de nó.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 13842512c7d983a70b32132b4a16be37ac0b2b54
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050720"
---
# <a name="preview---azure-kubernetes-service-aks-node-image-upgrades"></a>Pré-visualização - Azure Kubernetes Service (AKS) upgrades de imagem de nó

A AKS suporta a atualização das imagens num nó para que esteja atualizado com as mais recentes atualizações de sistemas operativos e de tempo de execução. A AKS fornece uma nova imagem por semana com as mais recentes atualizações, pelo que é benéfico atualizar regularmente as imagens do seu nó para as funcionalidades mais recentes, incluindo patches Linux ou Windows. Este artigo mostra-lhe como atualizar as imagens do nó de cluster AKS, bem como como atualizar as imagens do node pool sem atualizar a versão de Kubernetes.

Se estiver interessado em saber mais sobre as imagens mais recentes fornecidas pela AKS, consulte as notas de lançamento da [AKS](https://github.com/Azure/AKS/releases) para mais detalhes.

Para obter informações sobre a atualização da versão Kubernetes para o seu cluster, consulte [atualizar um cluster AKS][upgrade-cluster].

## <a name="register-the-node-image-upgrade-preview-feature"></a>Registe a funcionalidade de pré-visualização da pré-visualização da imagem do nó

Para utilizar a funcionalidade de atualização da imagem do nó durante o período de pré-visualização, é necessário registar a funcionalidade.

```azurecli
# Register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "NodeImageUpgradePreview"
```

Levará vários minutos para que a inscrição esteja concluída. Utilize o seguinte comando para verificar se a funcionalidade está registada:

```azurecli
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodeImageUpgradePreview')].{Name:name,State:properties.state}"
```

Durante a pré-visualização, precisa da extensão CLI *de pré-visualização aks* para utilizar a atualização da imagem do nó. Utilize o comando [de adicionar extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
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

## <a name="next-steps"></a>Passos seguintes

- Consulte as notas de lançamento da [AKS](https://github.com/Azure/AKS/releases) para obter informações sobre as imagens mais recentes do nó.
- Saiba como atualizar a versão Kubernetes com [upgrade de um cluster AKS.][upgrade-cluster]
- [Aplicar atualizações de segurança e kernel aos nós Linux no Serviço Azure Kubernetes (AKS)][security-update]
- Saiba mais sobre várias piscinas de nós e como atualizar piscinas de nó com [Criar e gerir várias piscinas de nó.][use-multiple-node-pools]

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
