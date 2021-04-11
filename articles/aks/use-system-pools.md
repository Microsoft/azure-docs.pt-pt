---
title: Utilize piscinas de nó de sistema no Serviço Azure Kubernetes (AKS)
description: Saiba como criar e gerir piscinas de nó de sistema no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: c3c65d3a7316d431c57d9fb75775e271bf9f34ca
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223273"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Gerir piscinas de nó de sistema no Serviço Azure Kubernetes (AKS)

No Serviço Azure Kubernetes (AKS), os nós da mesma configuração são agrupados em *piscinas de nó.* As piscinas de nó contêm os VMs subjacentes que executam as suas aplicações. Piscinas de nó de sistema e piscinas de nó de utilizador são dois modos diferentes de piscina de nó para os seus clusters AKS. As piscinas de nó de sistema servem o principal propósito de hospedar cápsulas de sistema críticas tais como `CoreDNS` e `metrics-server` . As piscinas de nó de utilizador servem o principal propósito de hospedar as suas cápsulas de aplicação. No entanto, as cápsulas de aplicação podem ser agendadas nas piscinas de nó do sistema se desejar ter apenas uma piscina no seu cluster AKS. Todos os aglomerados AKS devem conter pelo menos uma piscina de nó de sistema com pelo menos um nó.

> [!Important]
> Se executar uma única piscina de nó de sistema para o seu cluster AKS em ambiente de produção, recomendamos que use pelo menos três nós para a piscina de nós.

## <a name="before-you-begin"></a>Antes de começar

* Precisa da versão 2.3.1 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere clusters AKS que suportam piscinas de nó de sistema.

* Consulte [quotas, restrições de tamanho de máquina virtual e disponibilidade da região no Serviço Azure Kubernetes (AKS)][quotas-skus-regions].
* O cluster AKS deve ser construído com conjuntos de balança de máquinas virtuais como o tipo VM e o balanceador de carga *Standard* SKU.
* O nome de uma piscina de nó só pode conter caracteres alfanuméricos minúsculos e deve começar com uma letra minúscula. Para piscinas de nól de Linux, o comprimento deve ter entre 1 e 12 caracteres. Para as piscinas de nó do Windows, o comprimento deve ter entre 1 e 6 caracteres.
* Uma versão API de 2020-03-01 ou superior deve ser utilizada para definir um modo de piscina de nó. Os clusters criados em versões API com mais de 2020-03-01 contêm apenas piscinas de nó de utilizador, mas podem ser migrados para conter piscinas de nó de sistema seguindo os passos do [modo de piscina de atualização](#update-existing-cluster-system-and-user-node-pools).
* O modo de piscina de nó é uma propriedade necessária e deve ser explicitamente definido ao utilizar modelos ARM ou chamadas API diretas.

## <a name="system-and-user-node-pools"></a>Piscinas de nó de sistema e de utilizador

Para um conjunto de nós do sistema, a AKS atribui automaticamente a etiqueta **kubernetes.azure.com/mode: sistema** aos seus nós. Isto faz com que a AKS prefira cápsulas de sistema de agendamento em piscinas de nós que contenham esta etiqueta. Esta etiqueta não o impede de agendar cápsulas de aplicação em piscinas de nó do sistema. No entanto, recomendamos que isole as cápsulas críticas do sistema das suas cápsulas de aplicação para evitar que as cápsulas de aplicação mal configuradas ou fraudulentas matem acidentalmente as cápsulas do sistema. Você pode impor este comportamento criando um conjunto de nós de sistema dedicado. Utilize a `CriticalAddonsOnly=true:NoSchedule` mancha para evitar que as cápsulas de aplicação sejam programadas nas piscinas de nó do sistema.

As piscinas de nó de sistema têm as seguintes restrições:

* Os tipos de piscinas do sistema devem ser o Linux.
* Os grupos de nó do utilizador osType podem ser Linux ou Windows.
* As piscinas do sistema devem conter pelo menos um nó e as piscinas de nó do utilizador podem conter zero ou mais nós.
* As piscinas de nó do sistema requerem um VM SKU de pelo menos 2 vCPUs e memória de 4GB. Mas não é recomendável que a série B seja rebentada.
* Recomenda-se um mínimo de dois nós 4 vCPUs (por exemplo, Standard_DS4_v2), especialmente para grandes clusters (réplicas múltiplas do Pod CoreDNS, add-ons 3-4+, etc.).
* Os conjuntos de nós do sistema devem suportar pelo menos 30 cápsulas, conforme descrito pela [fórmula de valor mínimo e máximo para as cápsulas][maximum-pods].
* Piscinas de nó no spot requerem piscinas de nó do utilizador.
* Adicionar um conjunto adicional de nó de sistema ou alterar qual o conjunto de nós é um conjunto de nós do sistema *NÃO* moverá automaticamente as cápsulas do sistema. As cápsulas do sistema podem continuar a funcionar na mesma piscina de nós, mesmo que a altere para uma piscina de nó de utilizador. Se eliminar ou reduzir as cápsulas do sistema de funcionamento de uma piscina de nó de nó que era anteriormente uma piscina de nó de sistema, essas cápsulas de sistema são redistribuídas com agendamento preferido para o novo conjunto de nós do sistema.

Pode fazer as seguintes operações com piscinas de nó:

* Crie uma piscina de nó de sistema dedicada (prefira agendar cápsulas de sistema a piscinas de nó `mode:system` de)
* Altere uma piscina de nó de sistema para ser uma piscina de nó de utilizador, desde que tenha outra piscina de nó do sistema para ocupar o seu lugar no cluster AKS.
* Mude a piscina do nó do utilizador para ser uma piscina de nó do sistema.
* Elimine as piscinas de nó do utilizador.
* Pode eliminar as piscinas de nó do sistema, desde que tenha outra piscina de nó de sistema para ocupar o seu lugar no cluster AKS.
* Um cluster AKS pode ter várias piscinas de nó do sistema e requer pelo menos uma piscina de nó de sistema.
* Se quiser alterar várias configurações imutáveis nas piscinas de nó existentes, pode criar novos conjuntos de nós para as substituir. Um exemplo é adicionar um novo conjunto de nós com uma nova definição maxPods e eliminar a velha piscina de nós.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Criar um novo cluster AKS com uma piscina de nó de sistema

Quando cria um novo cluster AKS, cria automaticamente um conjunto de nó de sistema com um único nó. A piscina inicial do nó predefinido para um sistema de tipo. Quando cria novos conjuntos de nós `az aks nodepool add` com, esses nós são piscinas de nó do utilizador, a menos que especifique explicitamente o parâmetro do modo.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *leste.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utilize o comando [az aks create][az-aks-create] para criar um cluster AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um conjunto de sistema dedicado contendo um nó. Para as suas cargas de trabalho de produção, certifique-se de que está a utilizar piscinas de nó do sistema com pelo menos três nós. Esta operação pode demorar vários minutos a ser concluída.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Adicione uma piscina de nó de sistema dedicada a um cluster AKS existente

> [!Important]
> Não é possível alterar as manchas de nó através do CLI após a criação da piscina de nós.

Pode adicionar uma ou mais piscinas de nó de sistema aos clusters AKS existentes. É aconselhável agendar as suas cápsulas de aplicação em piscinas de nó de utilizador e dedicar piscinas de nó do sistema apenas a cápsulas críticas do sistema. Isto evita que as cápsulas de aplicação fraudulentas matem acidentalmente as cápsulas do sistema. Aplique este comportamento com a `CriticalAddonsOnly=true:NoSchedule` [mancha][aks-taints] para as piscinas de nó do sistema. 

O seguinte comando adiciona um conjunto de nó dedicado do sistema tipo de modo com uma contagem padrão de três nós.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Mostre detalhes para a sua piscina de nó

Pode verificar os detalhes da sua piscina de nó com o seguinte comando.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Um modo de tipo **Sistema** é definido para piscinas de nó de sistema, e um modo de tipo **utilizador** é definido para piscinas de nó de utilizador. Para uma piscina de sistema, verifique se a mancha está definida para , o `CriticalAddonsOnly=true:NoSchedule` que evitará que as cápsulas de aplicação sejam programadas nesta piscina de nós.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Atualizar o sistema de cluster existente e as piscinas de nó de utilizador

> [!NOTE]
> Uma versão API de 2020-03-01 ou superior deve ser utilizada para definir um modo de piscina de nó do sistema. Os clusters criados em versões API com mais de 2020-03-01 contêm apenas piscinas de nó de utilizador como resultado. Para receber a funcionalidade do grupo de nó do sistema e os benefícios em clusters mais antigos, atualize o modo de piscinas de nó existentes com os seguintes comandos na versão mais recente do Azure CLI.

Pode alterar modos tanto para grupos de sistema como para grupos de nó de utilizador. Só pode alterar uma piscina de nó de sistema para um pool de utilizadores se já existir outro conjunto de nós do sistema no cluster AKS.

Este comando altera uma piscina de nó de sistema para uma piscina de nó de utilizador.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Este comando altera uma piscina de nó de utilizador para uma piscina de nó do sistema.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Eliminar uma piscina de nó de sistema

> [!Note]
> Para utilizar os conjuntos de nó de sistema nos clusters AKS antes da versão API 2020-03-02, adicione um novo conjunto de nós do sistema e, em seguida, elimine o conjunto de nós padrão original.

Deve ter pelo menos duas piscinas de nó de sistema no seu cluster AKS antes de poder eliminar uma delas.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para eliminar o cluster, utilize o comando de eliminação do [grupo AZ][az-group-delete] para eliminar o grupo de recursos AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a criar e gerir piscinas de nó de sistema em um cluster AKS. Para obter mais informações sobre como usar várias piscinas de nó, consulte [a utilização de várias piscinas de nó.][use-multiple-node-pools]

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
