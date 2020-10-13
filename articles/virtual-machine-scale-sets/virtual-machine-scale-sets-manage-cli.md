---
title: Gerir conjuntos de balança de máquinas virtuais com o Azure CLI
description: O Common Azure CLI ordena a gestão de conjuntos de escala de máquina virtual, tais como como iniciar e parar uma instância, ou alterar a capacidade definida pela escala.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d954f7cdda4cae65f822489828226e0364d0fc29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570523"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Gerir uma balança de máquina virtual definida com o Azure CLI
Ao longo do ciclo de vida dos conjuntos de dimensionamento de máquinas virtuais, poderá ter de executar uma ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizam várias tarefas do ciclo de vida. Este artigo detalha alguns dos comandos comuns do Azure CLI que lhe permitem executar estas tarefas.

Para completar estas tarefas de gestão, precisa do mais recente Azure CLI. Para obter informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli). Se precisar de criar um conjunto de escala de máquina virtual, pode [criar um conjunto de escala com o Azure CLI](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Ver informações sobre um conjunto de escala
Para ver a informação geral sobre um conjunto de escala, use [az vmss show](/cli/azure/vmss). O exemplo a seguir obtém informações sobre o conjunto de escala nomeado *myScaleSet* no grupo de recursos *myResourceGroup.* Insira os seus próprios nomes da seguinte forma:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Ver VMs num conjunto de dimensionamento
Para visualizar uma lista de instâncias VM num conjunto de escala, utilize [as listas az vmss](/cli/azure/vmss). O exemplo a seguir lista todas as instâncias VM no conjunto de escala nomeado *myScaleSet* no grupo de recursos *myResourceGroup.* Forneça os seus próprios valores para estes nomes:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Para visualizar informações adicionais sobre uma instância VM específica, adicione o `--instance-id` parâmetro à [az vmss get-instance-view](/cli/azure/vmss) e especifique um caso para visualizar. O exemplo a seguir visualiza informações sobre a instância *VM 0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Insira os seus próprios nomes da seguinte forma:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

Também pode obter informações detalhadas *de exemploVer* informações para todas as instâncias numa chamada da API, o que pode ajudar a evitar o estrangulamento da API para grandes instalações. Forneça os seus próprios valores para `--resource-group` `--subscription` , e `--name` .

```azurecli
az vmss list-instances \
    --expand instanceView \
    --select instanceView \
    --resource-group <resourceGroupName> \
    --subscription <subID> \
    --name <vmssName>
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="list-connection-information-for-vms"></a>Listar informações de ligação para VMs
Para ligar aos VMs num conjunto de escala, é SSH ou RDP a um endereço IP público atribuído e número de porta. Por predefinição, as regras de tradução de endereços de rede (NAT) são adicionadas ao equilibrador de carga Azure que encaminha o tráfego de ligação remota para cada VM. Para listar o endereço e as portas para ligar a instâncias VM num conjunto de escala, utilize [a lista az vmss lista-informação de conexão .](/cli/azure/vmss) O exemplo a seguir lista as informações de ligação para instâncias VM no conjunto de escala denominado *myScaleSet* e no grupo de recursos *myResourceGroup.* Forneça os seus próprios valores para estes nomes:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostraram informações sobre o seu conjunto de escalas e as instâncias VM. Para aumentar ou diminuir o número de casos na escala definida, pode alterar a capacidade. O conjunto de escala cria ou remove o número necessário de VMs e, em seguida, configura os VMs para receber tráfego de aplicação.

Para ver o número de instâncias atualmente existentes num conjunto de dimensionamento, utilize [az vmss show](/cli/azure/vmss) e consulte *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Em seguida, pode aumentar ou reduzir manualmente o número de máquinas virtuais existentes no conjunto de dimensionamento com [az vmss scale](/cli/azure/vmss). O exemplo a seguir define o número de VMs na sua escala definida para *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

São necessários alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Se diminuir a capacidade de um conjunto de escala, os VMs com os IDs de instância mais elevados são removidos primeiro.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stop and start VMs in a scale set (Parar e iniciar VMs num conjunto de dimensionamento)
Para parar um ou mais VMs num conjunto de escala, utilize [a paragem az vmss](/cli/azure/vmss#az-vmss-stop). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser paradas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são paradas. Para parar vários VMs, separe cada identificação de instância com um espaço.

O exemplo a seguir para a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Os VMs parados continuam a ser atribuídos e continuam a incorrer em encargos computacional. Se, em vez disso, desejar que os VM sejam translocados e incorram apenas em taxas de armazenamento, utilize [o deallocato az vmss](/cli/azure/vmss). Para transparecer vários VMs, separe cada identificação de instância com um espaço. O exemplo a seguir para e desloca a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs em um conjunto de escala
Para iniciar um ou mais VMs num conjunto de escala, use [az vmss iniciar](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser iniciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. Para iniciar vários VMs, separe cada identificação de instância com um espaço.

O exemplo a seguir começa a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Restart VMs in a scale set (Reiniciar VMs num conjunto de dimensionamento)
Para reiniciar um ou mais VMs num conjunto de escala, [recomeça a az vmss](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser reinciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. Para reiniciar vários VMs, separe cada identificação de instância com um espaço.

O exemplo seguinte reinicia a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Remova VMs de um conjunto de escala
Para remover um ou mais VMs num conjunto de escala, utilize [instâncias de exclusão az vmss](/cli/azure/vmss). O `--instance-ids` parâmetro permite especificar um ou mais VMs para remover. Se especificar * para o exemplo de ID, todos os VMs no conjunto de escala são removidos. Para remover vários VMs, separe cada identificação de instância com um espaço.

O exemplo seguinte remove a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Passos seguintes
Outras tarefas comuns para conjuntos de escala incluem como [implementar uma aplicação,](virtual-machine-scale-sets-deploy-app.md)e [atualizar instâncias VM](virtual-machine-scale-sets-upgrade-scale-set.md). Também pode utilizar o Azure CLI para [configurar regras de escala automática](virtual-machine-scale-sets-autoscale-overview.md).
