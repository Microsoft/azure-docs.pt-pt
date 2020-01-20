---
title: Gerenciar conjuntos de dimensionamento de máquinas virtuais com o CLI do Azure
description: Os comandos comuns do CLI do Azure para gerenciar conjuntos de dimensionamento de máquinas virtuais, como como iniciar e parar uma instância, ou alterar a capacidade do conjunto de dimensionamento.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: d8f40708cf5a9989146a72e2093de7bad47ffde9
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275736"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Gerenciar um conjunto de dimensionamento de máquinas virtuais com o CLI do Azure
Ao longo do ciclo de vida dos conjuntos de dimensionamento de máquinas virtuais, poderá ter de executar uma ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizam várias tarefas do ciclo de vida. Este artigo detalha alguns dos comandos comuns do CLI do Azure que permitem executar essas tarefas.

Para concluir essas tarefas de gerenciamento, você precisa das CLI do Azure mais recentes. Para obter informações, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli). Se você precisar criar um conjunto de dimensionamento de máquinas virtuais, poderá [criar um conjunto de dimensionamento com o CLI do Azure](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Exibir informações sobre um conjunto de dimensionamento
Para exibir as informações gerais sobre um conjunto de dimensionamento, use [AZ vmss show](/cli/azure/vmss). O exemplo a seguir obtém informações sobre o conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos *MyResource* Group. Insira seus próprios nomes da seguinte maneira:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Ver VMs num conjunto de dimensionamento
Para exibir uma lista de instâncias de VM em um conjunto de dimensionamento, use [AZ vmss List-instances](/cli/azure/vmss). O exemplo a seguir lista todas as instâncias de VM no conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos *MyResource* Group. Forneça seus próprios valores para esses nomes:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Para exibir informações adicionais sobre uma instância específica de VM, adicione o parâmetro `--instance-id` para [AZ vmss Get-Instance-View](/cli/azure/vmss) e especifique uma instância a ser exibida. O exemplo a seguir exibe informações sobre a instância de VM *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Insira seus próprios nomes da seguinte maneira:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Listar informações de conexão para VMs
Para se conectar às VMs em um conjunto de dimensionamento, você SSH ou RDP para um número de porta e endereço IP público atribuído. Por padrão, as regras de conversão de endereços de rede (NAT) são adicionadas ao balanceador de carga do Azure que encaminha o tráfego de conexão remota para cada VM. Para listar o endereço e as portas para se conectar a instâncias de VM em um conjunto de dimensionamento, use [AZ vmss List-Instance-Connection-info](/cli/azure/vmss). O exemplo a seguir lista informações de conexão para instâncias de VM no conjunto de dimensionamento chamado *Myscaleset* e no grupo de recursos *MyResource* Group. Forneça seus próprios valores para esses nomes:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostraram informações sobre o conjunto de dimensionamento e as instâncias de VM. Para aumentar ou diminuir o número de instâncias no conjunto de dimensionamento, você pode alterar a capacidade. O conjunto de dimensionamento cria ou remove o número necessário de VMs e, em seguida, configura as VMs para receber o tráfego do aplicativo.

Para ver o número de instâncias atualmente existentes num conjunto de dimensionamento, utilize [az vmss show](/cli/azure/vmss) e consulte *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Em seguida, pode aumentar ou reduzir manualmente o número de máquinas virtuais existentes no conjunto de dimensionamento com [az vmss scale](/cli/azure/vmss). O exemplo a seguir define o número de VMs em seu conjunto de dimensionamento como *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

São necessários alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Se você diminuir a capacidade de um conjunto de dimensionamento, as VMs com as IDs de instância mais alta serão removidas primeiro.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Parar e iniciar VMs em um conjunto de dimensionamento
Para interromper uma ou mais VMs em um conjunto de dimensionamento, use [AZ vmss Stop](/cli/azure/vmss#az-vmss-stop). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser paradas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são paradas. Para interromper várias VMs, separe cada ID de instância com um espaço.

O exemplo a seguir interrompe a instância *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Forneça seus próprios valores da seguinte maneira:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

As VMs interrompidas permanecem alocadas e continuam a incorrer em encargos de computação. Se, em vez disso, você quiser que as VMs sejam desalocadas e só incorrerem em encargos de armazenamento, use [AZ vmss DEALLOCATE](/cli/azure/vmss). Para desalocar várias VMs, separe cada ID de instância com um espaço. O exemplo a seguir interrompe e desaloca a instância *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Forneça seus próprios valores da seguinte maneira:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs em um conjunto de dimensionamento
Para iniciar uma ou mais VMs em um conjunto de dimensionamento, use [AZ vmss Start](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser iniciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. Para iniciar várias VMs, separe cada ID de instância com um espaço.

O exemplo a seguir inicia a instância *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Forneça seus próprios valores da seguinte maneira:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Reiniciar VMs em um conjunto de dimensionamento
Para reiniciar uma ou mais VMs em um conjunto de dimensionamento, use [AZ vmss Restart](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser reinciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. Para reiniciar várias VMs, separe cada ID de instância com um espaço.

O exemplo a seguir reinicia a instância *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Forneça seus próprios valores da seguinte maneira:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Remover VMs de um conjunto de dimensionamento
Para remover uma ou mais VMs em um conjunto de dimensionamento, use [AZ vmss Delete-instances](/cli/azure/vmss). O parâmetro `--instance-ids` permite que você especifique uma ou mais VMs a serem removidas. Se você especificar * para a ID da instância, todas as VMs no conjunto de dimensionamento serão removidas. Para remover várias VMs, separe cada ID de instância com um espaço.

O exemplo a seguir remove a instância *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Forneça seus próprios valores da seguinte maneira:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Passos seguintes
Outras tarefas comuns para conjuntos de dimensionamento incluem como [implantar um aplicativo](virtual-machine-scale-sets-deploy-app.md)e [atualizar instâncias de VM](virtual-machine-scale-sets-upgrade-scale-set.md). Você também pode usar CLI do Azure para [configurar regras de dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md).
