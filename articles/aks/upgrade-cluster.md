---
title: Atualizar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como atualizar um cluster do Serviço Azure Kubernetes (AKS) para obter as mais recentes funcionalidades e atualizações de segurança.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 7e9a47b7bda4cdb0ff6f1983bc884f7441a26d9b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207977"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Azure Kubernetes Service (AKS)

Como parte do ciclo de vida de um cluster AKS, muitas vezes precisa de fazer upgrade para a versão mais recente da Kubernetes. É importante que aplique os mais recentes lançamentos de segurança da Kubernetes ou atualize para obter as funcionalidades mais recentes. Este artigo mostra-lhe como atualizar os componentes principais ou um único conjunto de nós padrão num cluster AKS.

Para os clusters AKS que usam várias piscinas de nós, consulte [Atualização de uma piscina de nó em AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão Azure CLI 2.0.65 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

> [!WARNING]
> Uma atualização do cluster AKS dispara um cordão e um dreno dos seus nós. Se tiver uma quota de cálculo baixa disponível, a atualização pode falhar. Veja [o aumento das quotas](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para mais informações.
> Se estiver a executar a sua própria implementação de autoescalar de cluster, desative-o (pode dimensioná-lo para réplicas zero) durante a atualização, uma vez que existe a possibilidade de interferir com o processo de atualização. O autoscaler gerido trata-o automaticamente. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Verifique se há atualizações de cluster AKS disponíveis

Para verificar quais os lançamentos da Kubernetes disponíveis para o seu cluster, utilize o comando de [atualizações az aks.][az-aks-get-upgrades] Verifica o seguinte exemplo das atualizações disponíveis para o cluster denominado *myAKSCluster* no grupo de recursos chamado *myResourceGroup:*

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Ao atualizar um cluster AKS, as versões menores da Kubernetes não podem ser ignoradas. Por exemplo, são permitidas atualizações entre *1.12.x* -> *1.13.x* ou *1.13.x* -> *1.14.x,* no entanto *1.12.x* -> *1.14.x* não é.
>
> Para atualizar, a partir de *1.12.x* -> *1.14.x*, primeira atualização de *1.12.x* -> *1.13.x,* em seguida, atualize a partir de *1.13.x* -> *1.14.x*.

A saída de exemplo seguinte mostra que o cluster pode ser atualizado para as versões *1.13.9* e *1.13.10:*

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Se não houver atualização disponível, obterá:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster do AKS

Com uma lista de versões disponíveis para o seu cluster AKS, use o comando de [upgrade az aks][az-aks-upgrade] para atualizar. Durante o processo de atualização, o AKS adiciona um novo nó ao cluster que executa a versão especificada de Kubernetes, em seguida, [cuidadosamente cordon e drena][kubernetes-drain] um dos nódosos antigos para minimizar a perturbação nas aplicações de execução. Quando o novo nó é confirmado como cápsulas de aplicação em execução, o nó antigo é apagado. Este processo repete-se até que todos os nós do cluster tenham sido atualizados.

O exemplo seguinte atualiza um cluster para a versão *1.13.10:*

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Leva alguns minutos para atualizar o cluster, dependendo de quantos nós você tem. 

> [!NOTE]
> Há um tempo total permitido para que um upgrade de cluster seja concluído. Este tempo é calculado tomando o produto de `10 minutes * total number of nodes in the cluster`. Por exemplo, num cluster de 20 nós, as operações de atualização devem ter sucesso em 200 minutos ou o AKS falhará a operação para evitar um estado de cluster não recuperável. Para recuperar a falha de atualização, tente novamente a operação de atualização após o tempo de paragem.

Para confirmar que a atualização foi bem sucedida, use o comando [do az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo que se segue mostra que o cluster funciona agora em *1.13.10:*

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como atualizar um cluster AKS existente. Para saber mais sobre a implantação e gestão de clusters AKS, consulte o conjunto de tutoriais.

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
