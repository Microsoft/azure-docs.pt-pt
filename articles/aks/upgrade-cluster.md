---
title: Atualizar um cluster do AKS (serviço kubernetes do Azure)
description: Saiba como atualizar um cluster do AKS (serviço kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 9404888eadf94eaf86a6e8584b49595e10b34c69
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264185"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do AKS (serviço kubernetes do Azure)

Como parte do ciclo de vida de um cluster AKS, você geralmente precisa atualizar para a versão mais recente do kubernetes. É importante que você aplique as últimas versões de segurança do kubernetes ou atualize para obter os recursos mais recentes. Este artigo mostra como atualizar os componentes mestres ou um único pool de nós padrão em um cluster AKS.

Para clusters AKS que usam vários pools de nó ou nós do Windows Server (atualmente em visualização no AKS), consulte [atualizar um pool de nós no AKs][nodepool-upgrade].

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.65 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

> [!WARNING]
> Uma atualização do cluster AKS dispara um Cordon e dreno de seus nós. Se você tiver uma cota de computação baixa disponível, a atualização poderá falhar.  Consulte [aumentar cotas](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request?branch=pr-en-us-83289) para obter mais informações.

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificar atualizações de cluster do AKS disponíveis

Para verificar quais versões do kubernetes estão disponíveis para o cluster, use o comando [AZ AKs Get-upgrades][az-aks-get-upgrades] . O exemplo a seguir verifica se há atualizações disponíveis para o cluster chamado *myAKSCluster* no grupo de recursos chamado *MyResource*Group:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando você atualiza um cluster AKS, as versões secundárias do kubernetes não podem ser ignoradas. Por exemplo, as atualizações entre *1.12. x* -> *1.13. x* ou *1.13. x* -> *1.14.* x são permitidas, no entanto *1.12. x* -> *1.14. x* não é.
>
> Para atualizar, de *1.12. x* -> *1.14.* x, primeiro atualize de *1.12. x* -> *1.13. x*e, em seguida, atualize de *1.13. x* -> *1.14. x*.

A saída de exemplo a seguir mostra que o cluster pode ser atualizado para as versões *1.13.9* e *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Se nenhuma atualização estiver disponível, você receberá:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster do AKS

Com uma lista de versões disponíveis para o cluster AKS, use o comando [AZ AKs upgrade][az-aks-upgrade] para atualizar. Durante o processo de atualização, o AKS adiciona um novo nó ao cluster que executa a versão especificada do kubernetes, depois cuidadosamente [Cordon e esvazia][kubernetes-drain] um dos nós antigos para minimizar a interrupção na execução de aplicativos. Quando o novo nó é confirmado como executando pods de aplicativo, o nó antigo é excluído. Esse processo se repete até que todos os nós no cluster tenham sido atualizados.

O exemplo a seguir atualiza um cluster para a versão *1.13.10*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Leva alguns minutos para atualizar o cluster, dependendo de quantos nós você tem. 

> [!NOTE]
> Há um tempo total permitido para a conclusão de uma atualização de cluster. Esse tempo é calculado por meio do produto do `10 minutes * total number of nodes in the cluster`. Por exemplo, em um cluster de 20 nós, as operações de atualização devem ter êxito em 200 minutos ou AKS falhará na operação para evitar um estado de cluster irrecuperável. Para recuperar em caso de falha de atualização, repita a operação de atualização depois que o tempo limite tiver sido atingido.

Para confirmar que a atualização foi bem-sucedida, use o comando [AZ AKs show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo a seguir mostra que o cluster agora executa *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou como atualizar um cluster AKS existente. Para saber mais sobre como implantar e gerenciar clusters AKS, confira o conjunto de tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais do AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
