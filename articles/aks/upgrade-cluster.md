---
title: Atualizar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como atualizar um cluster do Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072753"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Azure Kubernetes Service (AKS)

Como parte do ciclo de vida de um cluster do AKS, muitas vezes, terá de atualizar para a versão mais recente do Kubernetes. É importante que se aplicam as versões de segurança mais recentes do Kubernetes ou atualizar para obter as funcionalidades mais recentes. Este artigo mostra-lhe como atualizar um cluster do AKS existente.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer a execução da versão 2.0.56 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Verifique a existência de atualizações de cluster do AKS disponíveis

Para verificar quais versões do Kubernetes estão disponíveis para o seu cluster, utilize o [az aks get-atualizações] [ az-aks-get-upgrades] comando. O exemplo seguinte verifica a existência de atualizações disponíveis para o cluster com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Ao atualizar um cluster do AKS, Kubernetes a versões secundárias não podem ser ignorada. Por exemplo, é atualizado entre *1.10.x* -> *1.11.x* ou *1.11.x* -> *1.12.x* são permitidos, no entanto *1.10.x* -> *1.12.x* não é.
>
> Para atualizar, partir *1.10.x* -> *1.12.x*, a primeira atualização do *1.10.x* -> *1.11.x*, em seguida, atualize partir *1.11.x* -> *1.12.x*.

O resultado de exemplo seguinte mostra que o cluster pode ser atualizado para a versão *1.11.5* ou *1.11.6*:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster do AKS

Com uma lista de versões disponíveis para o seu cluster do AKS, utilize o [az aks upgrade] [ az-aks-upgrade] comando para atualizar. Durante o processo de atualização AKS adiciona um novo nó ao cluster, em seguida, cuidadosamente [cordon e avança] [ kubernetes-drain] um nó por vez para minimizar a interrupção de aplicações em execução. O exemplo seguinte atualiza um cluster para a versão *1.11.6*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

Demora alguns minutos a atualizar o cluster, dependendo da quantidade de nós que tem.

Para confirmar que a atualização foi concluída com êxito, utilize o [show do az aks] [ az-aks-show] comando:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

O resultado de exemplo seguinte mostra que o cluster é agora executada *1.11.6*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
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
