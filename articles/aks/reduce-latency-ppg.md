---
title: Utilize grupos de colocação de proximidade para reduzir a latência para clusters Azure Kubernetes Service (AKS)
description: Aprenda a usar grupos de colocação de proximidade para reduzir a latência para as cargas de trabalho do cluster AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c0c1bf83bf5a816debe61cab0ceab856bfbd062f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181181"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Reduzir a latência com grupos de colocação de proximidade

> [!Note]
> Ao utilizar grupos de colocação de proximidade em AKS, a colocação só se aplica aos nós do agente. O nó ao nó e a vagem hospedeira correspondente à latência do casulo é melhorada. A colocação não afeta a colocação do plano de controlo de um cluster.

Ao implementar a sua aplicação no Azure, espalhar instâncias da Máquina Virtual (VM) em regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação Azure estão fisicamente localizados perto uns dos outros. Algumas aplicações como jogos, simulações de engenharia e negociação de alta frequência (HFT) requerem baixa latência e tarefas que se completam rapidamente. Para cenários de computação de alto desempenho (HPC), como estes, considere usar [grupos de colocação](../virtual-machines/co-location.md#proximity-placement-groups) de proximidade (PPG) para as piscinas de nó do seu cluster.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão 2.14 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

### <a name="limitations"></a>Limitações

* Um grupo de colocação de proximidade pode mapear para no máximo uma zona de disponibilidade.
* Uma piscina de nó deve usar conjuntos de balança de máquina virtual para associar um grupo de colocação de proximidade.
* Uma piscina de nó pode associar um grupo de colocação de proximidade na piscina de nó criar apenas tempo.

## <a name="node-pools-and-proximity-placement-groups"></a>Piscinas de nó e grupos de colocação de proximidade

O primeiro recurso que implementa com um grupo de colocação de proximidade liga-se a um centro de dados específico. Os recursos adicionais mobilizados com o mesmo grupo de colocação de proximidade estão colocados no mesmo centro de dados. Uma vez que todos os recursos que usam o grupo de colocação de proximidade foram parados (deallocated) ou eliminados, ele não está mais ligado.

* Muitas piscinas de nó podem ser associadas a um único grupo de colocação de proximidade.
* Uma piscina de nó só pode ser associada a um único grupo de colocação de proximidade.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Configurar grupos de colocação de proximidade com zonas de disponibilidade

> [!NOTE]
> Embora os grupos de colocação de proximidade exijam uma piscina de nó para usar no máximo uma zona de disponibilidade, o [Azure VM SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) ainda está em vigor para VMs em uma única zona.

Os grupos de colocação de proximidade são um conceito de piscina de nó e associados a cada piscina de nó individual. A utilização de um recurso PPG não tem qualquer impacto na disponibilidade do avião de controlo AKS. Isto pode ter impacto na forma como um cluster deve ser projetado com zonas. Para garantir que um cluster é espalhado por várias zonas, recomenda-se o seguinte design.

* Provisione um cluster com o primeiro pool do sistema utilizando 3 zonas e nenhum grupo de colocação de proximidade associado. Isto garante que as cápsulas do sistema pousam numa piscina de nó dedicado que se espalhará por várias zonas.
* Adicione piscinas adicionais de nó de utilizador com uma zona única e grupo de colocação de proximidade associado a cada piscina. Um exemplo é o nodepool1 na zona 1 e PPG1, nodepool2 na zona 2 e PPG2, nodepool3 na zona 3 com PPG3. Isto garante, a nível de cluster, os nós são espalhados por várias zonas e cada piscina de nós individuais está na zona designada com um recurso PPG dedicado.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Criar um novo cluster AKS com um grupo de colocação de proximidade

O exemplo a seguir utiliza o [comando de criação do grupo AZ][az-group-create] para criar um grupo de recursos chamado *myResourceGroup* na região *central.* Um cluster AKS chamado *myAKSCluster* é então criado usando o comando [az aks create.][az-aks-create]

A ligação acelerada em rede melhora consideravelmente o desempenho em rede das máquinas virtuais. Idealmente, utilize grupos de colocação de proximidade em conjunto com a rede acelerada. Por padrão, a AKS utiliza redes aceleradas em [casos de máquinas virtuais suportadas](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), que incluem a maioria da máquina virtual Azure com dois ou mais vCPUs.

Crie um novo cluster AKS com um grupo de colocação de proximidade associado ao primeiro conjunto de nó de sistema:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Executar o seguinte comando, e armazenar o ID que é devolvido:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

O comando produz saída, que inclui o valor de *id* que precisa para os próximos comandos CLI:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Utilize o ID do grupo de colocação de proximidade para o valor *myPPGResourceID* no comando abaixo:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Adicione um grupo de colocação de proximidade a um cluster existente

Você pode adicionar um grupo de colocação de proximidade a um cluster existente, criando uma nova piscina de nós. Em seguida, pode migrar opcionalmente as cargas de trabalho existentes para a nova piscina de nós e, em seguida, apagar a piscina original do nó.

Utilize o mesmo grupo de colocação de proximidade que criou anteriormente, e isso garantirá que os nós de agente em ambos os nós em ambos os grupos de nós do seu cluster AKS estão fisicamente localizados no mesmo centro de dados.

Utilize o ID de recursos do grupo de colocação de proximidade que criou anteriormente, e adicione uma nova piscina de nó com o [`az aks nodepool add`][az-aks-nodepool-add] comando:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Limpeza

Para eliminar o cluster, utilize o [`az group delete`][az-group-delete] comando para eliminar o grupo de recursos AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [grupos de colocação de proximidade.][proximity-placement-groups]

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete