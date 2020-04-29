---
title: Gerir conjuntos de escala de máquina virtual com o CLI Azure
description: O ClI Comum Azure ordena gerir conjuntos de escala de máquinavirtual, tais como como iniciar e parar uma instância, ou alterar a capacidade definida pela escala.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: ad07a237dd40d836705b332c6c10356720901481
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81011587"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Gerir um conjunto de máquinas virtuais com o Azure CLI
Ao longo do ciclo de vida dos conjuntos de dimensionamento de máquinas virtuais, poderá ter de executar uma ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizam várias tarefas do ciclo de vida. Este artigo detalha alguns dos comandos comuns do Azure CLI que lhe permitem executar estas tarefas.

Para completar estas tarefas de gestão, precisa do mais recente Azure CLI. Para obter informações, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli). Se precisar de criar um conjunto de escala de máquina virtual, pode [criar um conjunto de escala com o Azure CLI](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Ver informações sobre um conjunto de escala
Para ver a informação geral sobre um conjunto de escala, use [az vmss show](/cli/azure/vmss). O exemplo seguinte obtém informações sobre o conjunto de escala sem nome *myScaleSet* no grupo de recursos *myResourceGroup.* Insira os seus próprios nomes da seguinte forma:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Ver VMs num conjunto de dimensionamento
Para ver uma lista de vm num conjunto de escala, utilize [az vmss list-instances](/cli/azure/vmss). O exemplo seguinte lista todas as instâncias vM no conjunto de escala nomeado *myScaleSet* no grupo de recursos *myResourceGroup.* Forneça os seus próprios valores para estes nomes:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Para ver informações adicionais sobre uma `--instance-id` instância de VM específica, adicione o parâmetro à [az vmss get-instance-view](/cli/azure/vmss) e especifique uma instância para visualizar. O exemplo seguinte vê informações sobre a instância *VM 0* no conjunto de escala sem nome *myScaleSet* e o grupo de recursos *myResourceGroup.* Insira os seus próprios nomes da seguinte forma:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Lista rés em relação a vms
Para ligar aos VMs num conjunto de escala, o SSH ou rdP a um endereço IP público e número de porta atribuídos. Por predefinição, as regras de tradução de endereços de rede (NAT) são adicionadas ao equilíbrio de carga Azure que reencaminha o tráfego de ligação remota a cada VM. Para listar o endereço e as portas para ligar às instâncias VM num conjunto de escala, utilize [az vmss list-instância-connection-info](/cli/azure/vmss). O exemplo seguinte lista as informações de ligação para instâncias VM no conjunto de escala sem nome *myScaleSet* e no grupo de recursos *myResourceGroup.* Forneça os seus próprios valores para estes nomes:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostraram informações sobre o seu conjunto de escala e os casos vm. Para aumentar ou diminuir o número de ocorrências no conjunto de escala, pode alterar a capacidade. O conjunto de escala cria ou remove o número necessário de VMs, em seguida, configura os VMs para receber o tráfego de aplicações.

Para ver o número de instâncias atualmente existentes num conjunto de dimensionamento, utilize [az vmss show](/cli/azure/vmss) e consulte *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Em seguida, pode aumentar ou reduzir manualmente o número de máquinas virtuais existentes no conjunto de dimensionamento com [az vmss scale](/cli/azure/vmss). O exemplo que se segue define o número de VMs na sua escala definida para *5:*

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

São necessários alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Se diminuir a capacidade de um conjunto de escala, os VMs com os IDs de maior instância são removidos primeiro.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stop and start VMs in a scale set (Parar e iniciar VMs num conjunto de dimensionamento)
Para parar um ou mais VMs num conjunto de escala, utilize [az vmss stop](/cli/azure/vmss#az-vmss-stop). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser paradas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são paradas. Para parar vários VMs, separe cada id de instância com um espaço.

O exemplo seguinte para a instância *0* no conjunto de escala sem nome *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Os VMs parados continuam a ser atribuídos e continuam a incorrer em encargos computação. Se, em vez disso, desejar que os VMs sejam transferidos e apenas incorram em taxas de armazenamento, utilize [az vmss desalocado](/cli/azure/vmss). Para desalojar vários VMs, separe cada id de cada instância com um espaço. O exemplo seguinte para e desafeta a instância *0* no conjunto de escala sem nome *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs em um conjunto de escala
Para iniciar um ou mais VMs num conjunto de escala, use [az vmss start](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser iniciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. Para iniciar vários VMs, separe cada id de cada instância com um espaço.

O exemplo seguinte começa a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Restart VMs in a scale set (Reiniciar VMs num conjunto de dimensionamento)
Para reiniciar um ou mais VMs num conjunto de escala, utilize o [reinício az vmss](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser reinciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. Para reiniciar vários VMs, separe cada id de cada instância com um espaço.

O exemplo seguinte reinicia a instância *0* no conjunto de escala sem nome *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Remover VMs de um conjunto de escala
Para remover um ou mais VMs num conjunto de escala, utilize [az vmss delete-instances](/cli/azure/vmss). O `--instance-ids` parâmetro permite especificar um ou mais VMs para remover. Se especificar * para a definição de ID da instância, todos os VMs no conjunto de escala são removidos. Para remover vários VMs, separe cada id de instância com um espaço.

O exemplo seguinte remove a instância *0* no conjunto de escala sem nome *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Passos seguintes
Outras tarefas comuns para conjuntos de escala incluem como [implementar uma aplicação](virtual-machine-scale-sets-deploy-app.md)e [atualizar instâncias vm](virtual-machine-scale-sets-upgrade-scale-set.md). Também pode utilizar o Azure CLI para [configurar regras de escala automática.](virtual-machine-scale-sets-autoscale-overview.md)
