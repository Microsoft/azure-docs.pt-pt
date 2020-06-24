---
title: Utilize piscinas de nó de sistema no Serviço Azure Kubernetes (AKS)
description: Saiba como criar e gerir piscinas de nó de sistema no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.openlocfilehash: 9b6270f81e7af8bd508d29510698e6cf9a5a2010
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052650"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Gerir piscinas de nó de sistema no Serviço Azure Kubernetes (AKS)

No Serviço Azure Kubernetes (AKS), os nós da mesma configuração são agrupados em *piscinas de nó.* As piscinas de nó contêm os VMs subjacentes que executam as suas aplicações. Piscinas de nó de sistema e piscinas de nó de utilizador são dois modos diferentes de piscina de nó para os seus clusters AKS. As piscinas de nó de sistema servem o principal propósito de hospedar cápsulas de sistema críticas, tais como CoreDNS e frente de túneis. As piscinas de nó de utilizador servem o principal propósito de hospedar as suas cápsulas de aplicação. No entanto, as cápsulas de aplicação podem ser agendadas nas piscinas de nó do sistema se desejar ter apenas uma piscina no seu cluster AKS. Todos os aglomerados AKS devem conter pelo menos uma piscina de nó de sistema com pelo menos um nó.

> [!Important]
> Se executar uma única piscina de nó de sistema para o seu cluster AKS em ambiente de produção, recomendamos que use pelo menos três nós para a piscina de nós.

## <a name="before-you-begin"></a>Antes de começar

* Precisa da versão 2.3.1 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere clusters AKS que suportam piscinas de nó de sistema.

* Consulte [quotas, restrições de tamanho de máquina virtual e disponibilidade da região no Serviço Azure Kubernetes (AKS)][quotas-skus-regions].
* O cluster AKS deve ser construído com conjuntos de balança de máquina virtual como o tipo VM.
* O nome de uma piscina de nó só pode conter caracteres alfanuméricos minúsculos e deve começar com uma letra minúscula. Para piscinas de nól de Linux, o comprimento deve ter entre 1 e 12 caracteres. Para as piscinas de nó do Windows, o comprimento deve ter entre 1 e 6 caracteres.
* Uma versão API de 2020-03-01 ou superior deve ser utilizada para definir um modo de piscina de nó. Os clusters criados em versões API com mais de 2020-03-01 contêm apenas piscinas de nó de utilizador, mas podem ser migrados para conter piscinas de nó de sistema seguindo os passos do [modo de piscina de atualização](#update-existing-cluster-system-and-user-node-pools).
* O modo de piscina de nó é uma propriedade necessária e deve ser explicitamente definido ao utilizar modelos ARM ou chamadas API diretas.

## <a name="system-and-user-node-pools"></a>Piscinas de nó de sistema e de utilizador

Os nós de piscina de nós de nó do sistema têm a etiqueta **kubernetes.azure.com/mode: sistema**. Cada cluster AKS contém pelo menos uma piscina de nós de sistema. As piscinas de nó de sistema têm as seguintes restrições:

* Os tipos de piscinas do sistema devem ser o Linux.
* Os grupos de nó do utilizador osType podem ser Linux ou Windows.
* As piscinas do sistema devem conter pelo menos um nó e as piscinas de nó do utilizador podem conter zero ou mais nós.
* As piscinas de nó do sistema requerem um VM SKU de pelo menos 2 vCPUs e memória de 4GB.
* Os conjuntos de nós do sistema devem suportar pelo menos 30 cápsulas, conforme descrito pela [fórmula de valor mínimo e máximo para as cápsulas][maximum-pods].
* Piscinas de nó no spot requerem piscinas de nó do utilizador.

Pode fazer as seguintes operações com piscinas de nó:

* Altere uma piscina de nó de sistema para ser uma piscina de nó de utilizador, desde que tenha outra piscina de nó do sistema para ocupar o seu lugar no cluster AKS.
* Mude a piscina do nó do utilizador para ser uma piscina de nó do sistema.
* Elimine as piscinas de nó do utilizador.
* Pode eliminar as piscinas de nó do sistema, desde que tenha outra piscina de nó de sistema para ocupar o seu lugar no cluster AKS.
* Um cluster AKS pode ter várias piscinas de nó do sistema e requer pelo menos uma piscina de nó de sistema.
* Se quiser alterar várias configurações imutáveis nas piscinas de nó existentes, pode criar novos conjuntos de nós para as substituir. Um exemplo é adicionar um novo conjunto de nós com uma nova definição maxPods e eliminar a velha piscina de nós.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Criar um novo cluster AKS com uma piscina de nó de sistema

Quando cria um novo cluster AKS, cria automaticamente um conjunto de nó de sistema com um único nó. A piscina inicial do nó predefinido para um sistema de tipo. Quando cria novas piscinas de nó com az aks nodepool adicionar, esses nós são piscinas de nó do utilizador, a menos que especifique explicitamente o parâmetro do modo.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *leste.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utilize o comando [az aks create][az-aks-create] para criar um cluster AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com uma piscina de sistema contendo um nó. Para as suas cargas de trabalho de produção, certifique-se de que está a utilizar piscinas de nó do sistema com pelo menos três nós. Esta operação pode demorar vários minutos a ser concluída.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Adicione uma piscina de nó de sistema a um cluster AKS existente

Pode adicionar uma ou mais piscinas de nó de sistema aos clusters AKS existentes. O seguinte comando adiciona um conjunto de nó do sistema tipo de modo com uma contagem predefinida de três nós.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Mostre detalhes para a sua piscina de nó

Pode verificar os detalhes da sua piscina de nó com o seguinte comando.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Um modo de tipo **Sistema** é definido para piscinas de nó de sistema, e um modo de tipo **utilizador** é definido para piscinas de nó de utilizador.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
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

Anteriormente não foi possível eliminar o conjunto de nós do sistema, que era o conjunto inicial de nó padrão num cluster AKS. Tem agora a flexibilidade para eliminar qualquer piscina de nós dos seus clusters. Uma vez que os clusters AKS requerem pelo menos uma piscina de nós do sistema, você deve ter pelo menos duas piscinas de nó de sistema no seu cluster AKS antes de poder eliminar um deles.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
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
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
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
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node