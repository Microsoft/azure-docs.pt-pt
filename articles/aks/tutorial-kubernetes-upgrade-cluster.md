---
title: Tutorial do Kubernetes no Azure - Atualizar cluster para versão superior
description: Neste tutorial do Azure Kubernetes Service (AKS), saiba como atualizar um cluster do AKS existente para a versão do Kubernetes mais recente disponível.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 0986da64fda659b949d5d1e6cfae03df2daded19
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019148"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Tutorial: Atualizar kubernetes no serviço kubernetes do Azure (AKS)

Como parte da aplicação e do ciclo de vida do cluster, pode querer atualizar para a versão mais recente disponível do Kubernetes e utilizar novas funcionalidades. Um cluster do Serviço Kubernetes do Azure (AKS) poder ser atualizado através da CLI do Azure.

Neste tutorial, parte sete de sete, é atualizado um cluster do Kubernetes. Saiba como:

> [!div class="checklist"]
> * Identificar versões do Kubernetes atuais e disponíveis
> * Atualizar os nós do Kubernetes
> * Validar uma atualização com êxito

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo era empacotado em uma imagem de contêiner. Essa imagem foi carregada no registro de contêiner do Azure e você criou um cluster AKS. O aplicativo foi então implantado no cluster AKS. Se você ainda não realizou essas etapas e gostaria de acompanhar, comece com o [tutorial 1 – criar imagens de contêiner][aks-tutorial-prepare-app].

Este tutorial requer que você esteja executando o CLI do Azure versão 2.0.53 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Obter versões de cluster disponíveis

Antes de atualizar um cluster, utilize o comando [az aks get-upgrades][] para verificar que versões do Kubernetes estão disponíveis para atualização:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

No exemplo a seguir, a versão atual é *1.13.9*e as versões disponíveis são mostradas na coluna *atualizações* .

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.13.9           1.13.9             1.14.5
```

## <a name="upgrade-a-cluster"></a>Atualizar um cluster

Para minimizar a interrupção na execução de aplicativos, os nós AKS são cuidadosamente isolados e drenados. Nesse processo, as seguintes etapas são executadas:

1. O Agendador kubernetes impede que os pods adicionais sejam agendados em um nó a ser atualizado.
1. A execução de pods no nó é agendada em outros nós no cluster.
1. É criado um nó que executa os componentes mais recentes do kubernetes.
1. Quando o novo nó estiver pronto e ingressado no cluster, o Agendador kubernetes começará a executar pods nele.
1. O nó antigo é excluído e o próximo nó do cluster inicia o Cordon e o processo de drenagem.

Utilize o comando [az aks upgrade][] para atualizar o cluster do AKS. O exemplo a seguir atualiza o cluster para kubernetes versão *1.14.5*.

> [!NOTE]
> Pode atualizar apenas uma versão secundária de cada vez. Por exemplo, você pode atualizar de *1.12. x* para *1.13. x*, mas não pode atualizar de *1.12.8* para *1.14. x* diretamente. Para atualizar de *1.12. x* para *1.14. x*, primeiro atualize de *1.12. x* para *1.13. x*e, em seguida, execute outra atualização de *1.13. x* para *1.14. x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.14.5
```

A seguinte saída de exemplo condensada mostra o *kubernetesVersion* Now reports *1.14.5*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.14.5",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Validar uma atualização

Confirme se a atualização foi concluída com êxito com o comando [az aks show][], da seguinte forma:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo a seguir mostra que o cluster AKS executa *KubernetesVersion 1.14.5*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.14.5               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Eliminar o cluster

Como este tutorial é a última parte da série, talvez você queira excluir o cluster AKS. Uma vez que os nós do Kubernetes são executados em máquinas virtuais (VMs) do Azure, continuam a incorrer em custos, mesmo que não utilize o cluster. Use o comando [AZ Group Delete][az-group-delete] para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter as etapas sobre como remover a entidade de serviço, consulte [considerações e exclusão da entidade de serviço AKs][sp-delete].

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, atualizou o Kubernetes num cluster do AKS. Aprendeu a:

> [!div class="checklist"]
> * Identificar versões do Kubernetes atuais e disponíveis
> * Atualizar os nós do Kubernetes
> * Validar uma atualização com êxito

Siga esta ligação para saber mais sobre o AKS.

> [!div class="nextstepaction"]
> [Visão geral do AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
