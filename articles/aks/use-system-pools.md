---
title: Utilize piscinas de nó de sistema no Serviço Azure Kubernetes (AKS)
description: Aprenda a criar e gerir piscinas de nós de sistema no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 04/28/2020
ms.openlocfilehash: 04322bdaa2e0e72c5fbdbadb07f2608ee360e1e3
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790563"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Gerir piscinas de nó de sistema no Serviço Azure Kubernetes (AKS)

No Serviço Azure Kubernetes (AKS), os nódosos da mesma configuração são agrupados em *piscinas*de nó . As piscinas de nó contêm os VMs subjacentes que executam as suas aplicações. Piscinas de nós de sistema e piscinas de nós de utilizador são dois modos diferentes de piscina de nós para os seus clusters AKS. As piscinas de nó do sistema servem o principal propósito de hospedar cápsulas de sistema críticos, tais como CoreDNS e tunnelfront. As piscinas de nó do utilizador servem o principal propósito de hospedar as suas cápsulas de aplicação. No entanto, as cápsulas de aplicação podem ser programadas em piscinas de nós do sistema se desejar ter apenas uma piscina no seu cluster AKS. Cada cluster AKS deve conter pelo menos uma piscina de nó de sistema com pelo menos um nó. 

> [!Important]
> Se você executar uma única piscina de nó de sistema para o seu cluster AKS em um ambiente de produção, recomendamos que você use pelo menos três nós para a piscina do nó.

## <a name="before-you-begin"></a>Antes de começar

* Precisa da versão 2.3.1 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere clusters AKS que suportam piscinas de nós do sistema.

* Ver Quotas, restrições de tamanho de máquina virtual e disponibilidade da [região no Serviço Azure Kubernetes (AKS)][quotas-skus-regions].
* O cluster AKS deve ser construído com conjuntos de escala de máquina virtual como o tipo VM.
* O nome de uma piscina de nó só pode conter caracteres alfanuméricos minúsculos e deve começar com uma letra minúscula. Para piscinas de nó linux, o comprimento deve ser entre 1 e 12 caracteres. Para piscinas de nó windows, o comprimento deve ser entre 1 e 6 caracteres.
* Uma versão API de 2020-03-01 ou superior deve ser utilizada para definir um modo de piscina de nó.
* O modo de uma piscina de nó é uma propriedade necessária e deve ser explicitamente definido quando utilizar modelos ARM ou chamadas API diretas.

## <a name="system-and-user-node-pools"></a>Piscinas de nó de sistema e utilizador

Os nós da piscina do nó do sistema têm o rótulo **kubernetes.azure.com/mode: sistema**. Cada cluster AKS contém pelo menos uma piscina de nó de sistema. As piscinas de nó do sistema têm as seguintes restrições:

* As piscinas do sistema ostype devem ser Linux.
* As piscinas de nó do utilizador ossistemas podem ser Linux ou Windows.
* As piscinas do sistema devem conter pelo menos um nó, e as piscinas de nós de utilizador podem conter zero ou mais nós.
* As piscinas de nós do sistema requerem um VM SKU de pelo menos 2 vCPUs e memória de 4GB.
* As piscinas de nó do sistema devem suportar pelo menos 30 cápsulas, conforme descrito pela fórmula de [valor mínimo e máximo para as cápsulas][maximum-pods].
* As piscinas de nó spot requerem piscinas de nó do utilizador.

Você pode fazer as seguintes operações com piscinas de nó:

* Mude uma piscina de nó do sistema para ser uma piscina de nó de utilizador, desde que tenha outra piscina de nó do sistema para ocupar o seu lugar no cluster AKS.
* Mude uma piscina de nó de utilizador para ser uma piscina de nó do sistema.
* Elimine as piscinas de nó do utilizador.
* Você pode eliminar piscinas de nós do sistema, desde que você tenha outra piscina de nó de sistema para tomar o seu lugar no cluster AKS.
* Um cluster AKS pode ter várias piscinas de nó do sistema e requer pelo menos uma piscina de nó de sistema.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Crie um novo cluster AKS com uma piscina de nó de sistema

Ao criar um novo cluster AKS, cria automaticamente uma piscina de nó do sistema com um único nó. O conjunto de nóinicial falha num modo de sistema de tipo. Quando cria novas piscinas de nó com az aks nodepool, essas piscinas de nós são piscinas de nós de utilizador, a menos que especifique explicitamente o parâmetro de modo.

O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na região *oriental.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utilize o comando [az aks create][az-aks-create] para criar um cluster AKS. O exemplo seguinte cria um cluster chamado *myAKSCluster* com um conjunto de sistemacontendo um nó. Para a sua carga de trabalho de produção, certifique-se de que está a utilizar piscinas de nós de sistema com pelo menos três nós. Esta operação pode demorar alguns minutos a ser concluída.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Adicione uma piscina de nó de sistema a um cluster AKS existente

Você pode adicionar uma ou mais piscinas de nó do sistema aos aglomerados AKS existentes. O comando seguinte adiciona um conjunto de nó de sistema tipo modo com uma contagem padrão de três nós.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Mostre detalhes para a sua piscina de nó

Pode verificar os detalhes da sua piscina de nó com o seguinte comando.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Um modo de tipo **Sistema** é definido para piscinas de nó do sistema, e um modo de tipo **User** é definido para piscinas de nós de utilizador.

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

## <a name="update-system-and-user-node-pools"></a>Sistema de atualização e piscinas de nó de utilizador

Pode alterar os modos tanto para piscinas de nós de sistema como para utilizador. Você pode mudar um conjunto de nó do sistema para uma piscina de utilizador apenas se outra piscina de nó de sistema já existir no cluster AKS.

Este comando muda uma piscina de nó do sistema para uma piscina de nó de utilizador.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Este comando muda uma piscina de nó do utilizador para uma piscina de nó do sistema.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Eliminar uma piscina de nó do sistema

> [!Note]
> Para utilizar piscinas de nó do sistema em clusters AKS antes da versão API 2020-03-02, adicione um novo conjunto de nó do sistema e, em seguida, elimine o conjunto de nós padrão original.

Anteriormente não podia excluir o conjunto de nódoo do sistema, que era o conjunto de nós padrão inicial num cluster AKS. Tem agora a flexibilidade para eliminar qualquer piscina de nós dos seus aglomerados. Uma vez que os clusters AKS requerem pelo menos uma piscina de nó de sistema, você deve ter pelo menos duas piscinas de nó de sistema no seu cluster AKS antes de poder apagar uma delas.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar e gerir piscinas de nós de sistema num cluster AKS. Para obter mais informações sobre como usar várias piscinas de nós, consulte a utilização de [várias piscinas][use-multiple-node-pools]de nós.

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