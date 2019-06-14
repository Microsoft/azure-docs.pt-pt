---
title: Gerir conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure | Documentos da Microsoft
description: Capacidade do conjunto de comandos comuns da CLI do Azure para gerir conjuntos de dimensionamento de máquinas virtuais, tais como iniciar e parar uma instância ou alterar a escala.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: b49182ebdcc93c4a51a55f27c3e0bf7a45307b7f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618086"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Gerir um conjunto de dimensionamento com a CLI do Azure
Ao longo do ciclo de vida dos conjuntos de dimensionamento de máquinas virtuais, poderá ter de executar uma ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizam várias tarefas do ciclo de vida. Este artigo fornece detalhes sobre alguns dos comandos da CLI do Azure comuns que permitem-lhe realizar estas tarefas.

Para concluir estas tarefas de gestão, tem a CLI do Azure mais recente. Para obter informações, consulte [instalar a CLI do Azure](/cli/azure/install-azure-cli). Se precisar de criar um conjunto de dimensionamento de máquina virtual, pode [criar um conjunto de dimensionamento com a CLI do Azure](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Ver informações sobre um conjunto de dimensionamento
Para ver as informações gerais sobre um conjunto de dimensionamento, utilize [show do az vmss](/cli/azure/vmss). O exemplo seguinte obtém informações sobre o conjunto nomeado de dimensionamento *myScaleSet* no *myResourceGroup* grupo de recursos. Introduza os seus próprios nomes da seguinte forma:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Ver VMs num conjunto de dimensionamento
Para ver uma lista de instância de VM num conjunto de dimensionamento, utilize [az vmss-instâncias de lista](/cli/azure/vmss). O exemplo seguinte lista todas as instâncias VM no conjunto nomeado de dimensionamento *myScaleSet* no *myResourceGroup* grupo de recursos. Fornece seus próprios valores para estes nomes:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Para ver informações adicionais sobre uma instância VM específica, adicione a `--instance-id` parâmetro [az vmss get-instance-view](/cli/azure/vmss) e especifique uma instância para ver. O exemplo seguinte mostra informações sobre a instância de VM *0* no conjunto nomeado de dimensionamento *myScaleSet* e o *myResourceGroup* grupo de recursos. Introduza os seus próprios nomes da seguinte forma:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Listar informações de ligação para VMs
Para ligar as VMs num conjunto de dimensionamento, SSH ou RDP para um atribuído público endereço IP e porta número. Por predefinição, as regras de tradução (NAT) de endereços de rede são adicionadas ao balanceador de carga do Azure que encaminha o tráfego de ligação remota para cada VM. Para listar os endereços e portas para ligar a instâncias VM num conjunto de dimensionamento, utilize [az vmss list-instance-ligação-info](/cli/azure/vmss). O exemplo a seguir lista as informações de ligação para instâncias de VM no conjunto nomeado de dimensionamento *myScaleSet* e, no *myResourceGroup* grupo de recursos. Fornece seus próprios valores para estes nomes:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostraram informações sobre o conjunto de dimensionamento e as instâncias de VM. Para aumentar ou diminuir o número de instâncias no conjunto de dimensionamento, pode alterar a capacidade. O conjunto de dimensionamento cria ou remove o número necessário de VMs, em seguida, configura as VMs para receber o tráfego de aplicativo.

Para ver o número de instâncias atualmente existentes num conjunto de dimensionamento, utilize [az vmss show](/cli/azure/vmss) e consulte *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Em seguida, pode aumentar ou reduzir manualmente o número de máquinas virtuais existentes no conjunto de dimensionamento com [az vmss scale](/cli/azure/vmss). O exemplo seguinte define o número de VMs no seu conjunto de dimensionamento para *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

São necessários alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Se diminuir a capacidade de uma escala definido, as VMs com a instância mais alta IDs são removidas primeiro.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Parar e iniciar VMs num conjunto de dimensionamento
Para parar uma ou mais VMs num conjunto de dimensionamento, utilize [stop do az vmss](/cli/azure/vmss#az-vmss-stop). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser paradas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são paradas. Para parar de várias VMs, separe cada ID de instância com um espaço.

O exemplo seguinte para a instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e o *myResourceGroup* grupo de recursos. Fornece seus próprios valores da seguinte forma:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

VMs paradas permanecem alocadas e continuam a incorrer em custos de computação. Se preferir que as VMs para ser desalocada e apenas incorrer em custos de armazenamento, utilize [az vmss deallocate](/cli/azure/vmss). Para desalocar várias VMs, separe cada ID de instância com um espaço. O exemplo a seguir para e desaloca a instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e o *myResourceGroup* grupo de recursos. Fornece seus próprios valores da seguinte forma:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs num conjunto de dimensionamento
Para iniciar uma ou mais VMs num conjunto de dimensionamento, utilize [az vmss iniciar](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser iniciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. Para começar a várias VMs, separe cada ID de instância com um espaço.

O exemplo seguinte inicia a instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e o *myResourceGroup* grupo de recursos. Fornece seus próprios valores da seguinte forma:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Reiniciar VMs num conjunto de dimensionamento
Para reiniciar uma ou mais VMs num conjunto de dimensionamento, utilize [az vmss reiniciar](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais VMs que deverão ser reinciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. Para reiniciar várias VMs, separe cada ID de instância com um espaço.

O seguinte exemplo reinicia a instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e o *myResourceGroup* grupo de recursos. Fornece seus próprios valores da seguinte forma:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Remover as VMs a partir de um conjunto de dimensionamento
Para remover uma ou mais VMs num conjunto de dimensionamento, utilize [az vmss delete-instâncias](/cli/azure/vmss). O `--instance-ids` parâmetro permite-lhe especificar uma ou mais VMs para remover. Se especificar * para a instância de ID, todas as VMs no conjunto de dimensionamento são removidos. Para remover várias VMs, separe cada ID de instância com um espaço.

O exemplo seguinte remove a instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e o *myResourceGroup* grupo de recursos. Fornece seus próprios valores da seguinte forma:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Passos Seguintes
Incluem outras tarefas comuns para conjuntos de dimensionamento como [implementar uma aplicação](virtual-machine-scale-sets-deploy-app.md), e [atualizar instâncias VM](virtual-machine-scale-sets-upgrade-scale-set.md). Também pode utilizar a CLI do Azure para [configurar regras de dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md).
