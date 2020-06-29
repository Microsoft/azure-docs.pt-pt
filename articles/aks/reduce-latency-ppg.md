---
title: Utilize grupos de colocação de proximidade para reduzir a latência para clusters Azure Kubernetes Service (AKS)
description: Aprenda a usar grupos de colocação de proximidade para reduzir a latência para as cargas de trabalho do cluster AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/22/2020
ms.openlocfilehash: 095746b9cf3cada9cebf7d169078eff9eb64a52d
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85444272"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>Reduzir a latência com grupos de colocação de proximidade (Pré-visualização)

> [!Note]
> Quando utiliza grupos de colocação de proximidade com AKS, a colocação aplica-se apenas aos nós do agente. O nó ao nó e a vagem hospedeira correspondente à latência do casulo é melhorada. A colocação não afeta a colocação do plano de controlo de um cluster.

Ao implementar a sua aplicação no Azure, espalhar instâncias da Máquina Virtual (VM) em regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação Azure estão fisicamente localizados perto uns dos outros. Algumas aplicações como jogos, simulações de engenharia e negociação de alta frequência (HFT) requerem baixa latência e tarefas que se completam rapidamente. Para cenários de computação de alto desempenho (HPC), como estes, considere usar [grupos de colocação](../virtual-machines/linux/co-location.md#proximity-placement-groups) de proximidade para as piscinas de nó do seu cluster.

## <a name="limitations"></a>Limitações

* O grupo de colocação de proximidade abrange uma única zona de disponibilidade.
* Não existe suporte atual para clusters AKS que usam conjuntos de disponibilidade de máquinas virtuais.
* Não é possível modificar piscinas de nó existentes para usar um grupo de colocação de proximidade.

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS estão disponíveis numa base de autosserviço, opt-in. As pré-visualizações são fornecidas "as-is" e "conforme disponível", e estão excluídas dos Contratos de Nível de Serviço e garantia limitada. As pré-visualizações da AKS são parcialmente cobertas pelo apoio ao cliente numa base de melhor esforço. Como tal, estas características não se destinam ao uso da produção. Para mais informações, consulte os seguintes artigos de apoio:
>
> - [Políticas de apoio da AKS](support-policies.md)
> - [FAQ de suporte Azure](faq.md)

## <a name="before-you-begin"></a>Antes de começar

Deve ter os seguintes recursos instalados:

- A extensão aks-preview 0.4.53

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>Configurar a funcionalidade de pré-visualização para grupos de colocação de proximidade

> [!IMPORTANT]
> Quando utiliza grupos de colocação de proximidade com AKS, a colocação aplica-se apenas aos nós do agente. O nó ao nó e a vagem hospedeira correspondente à latência do casulo é melhorada. A colocação não afeta a colocação do plano de controlo de um cluster.

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

Pode levar vários minutos para a inscrição. Utilize o comando abaixo para verificar se a funcionalidade está registada:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

Durante a pré-visualização, você precisa da extensão CLI *de pré-visualização aks* para usar grupos de colocação de proximidade. Utilize o comando [de adicionar extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
## <a name="node-pools-and-proximity-placement-groups"></a>Piscinas de nó e grupos de colocação de proximidade

O primeiro recurso que implementa com um grupo de colocação de proximidade liga-se a um centro de dados específico. Os recursos adicionais mobilizados com o mesmo grupo de colocação de proximidade estão colocados no mesmo centro de dados. Uma vez que todos os recursos que usam o grupo de colocação de proximidade foram parados (deallocated) ou eliminados, ele não está mais ligado.

* Muitas piscinas de nó podem ser associadas a um único grupo de colocação de proximidade.
* Uma piscina de nó só pode ser associada a um único grupo de colocação de proximidade.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Criar um novo cluster AKS com um grupo de colocação de proximidade

O exemplo a seguir utiliza o [comando de criação do grupo AZ][az-group-create] para criar um grupo de recursos chamado *myResourceGroup* na região *central.* Um cluster AKS chamado *myAKSCluster* é então criado usando o comando [az aks create.][az-aks-create] 

A ligação acelerada em rede melhora consideravelmente o desempenho em rede das máquinas virtuais. Idealmente, utilize grupos de colocação de proximidade em conjunto com a rede acelerada. Por padrão, a AKS utiliza redes aceleradas em [casos de máquinas virtuais suportadas](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), que incluem a maioria da máquina virtual Azure com dois ou mais vCPUs.

Criar um novo cluster AKS com um grupo de colocação de proximidade:

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
# Create an AKS cluster that uses a proximity placement group for the initial node pool
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
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete

