---
title: Atualizar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como atualizar um cluster do Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 2cadd4b33cb52307599ce1e83eee8370ef9850fe
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692772"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Azure Kubernetes Service (AKS)

Como parte do ciclo de vida de um cluster do AKS, muitas vezes, terá de atualizar para a versão mais recente do Kubernetes. É importante que se aplicam as versões de segurança mais recentes do Kubernetes ou atualizar para obter as funcionalidades mais recentes. Este artigo mostra-lhe como atualizar os componentes de principais ou de um único, o conjunto predefinido de nó num cluster do AKS.

Para o AKS clusters que utilizam vários conjuntos de nós ou nós do Windows Server (ambos atualmente em pré-visualização no AKS), consulte [atualizar um conjunto de nós no AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer a execução da versão 2.0.65 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Verifique a existência de atualizações de cluster do AKS disponíveis

Para verificar quais versões do Kubernetes estão disponíveis para o seu cluster, utilize o [az aks get-atualizações] [ az-aks-get-upgrades] comando. O exemplo seguinte verifica a existência de atualizações disponíveis para o cluster com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Ao atualizar um cluster do AKS, Kubernetes a versões secundárias não podem ser ignorada. Por exemplo, é atualizado entre *1.11.x* -> *1.12.x* ou *1.12.x* -> *1.13.x* são permitidos, no entanto *1.11.x* -> *1.13.x* não é.
>
> Para atualizar, partir *1.11.x* -> *1.13.x*, a primeira atualização do *1.11.x* -> *1.12.x*, em seguida, atualize partir *1.12.x* -> *1.13.x*.

O resultado de exemplo seguinte mostra que o cluster pode ser atualizado para a versão *1.12.7* ou *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster do AKS

Com uma lista de versões disponíveis para o seu cluster do AKS, utilize o [az aks upgrade] [ az-aks-upgrade] comando para atualizar. Durante o processo de atualização, o AKS adiciona um novo nó ao cluster que executa a versão especificada do Kubernetes, em seguida, cuidadosamente [cordon e avança] [ kubernetes-drain] um de nós do antigos para minimizar a interrupção para em execução aplicações. Quando o novo nó é confirmado como em execução pods de aplicação, o nó antigo é eliminado. O processo se repete até que todos os nós do cluster foram atualizados.

O exemplo seguinte atualiza um cluster para a versão *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Demora alguns minutos a atualizar o cluster, dependendo da quantidade de nós que tem.

Para confirmar que a atualização foi concluída com êxito, utilize o [show do az aks] [ az-aks-show] comando:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

O resultado de exemplo seguinte mostra que o cluster é agora executada *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo mostrou como atualizar um cluster do AKS existente. Para saber mais sobre como implementar e gerir clusters do AKS, ver o conjunto de tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
