---
title: Gerir conjuntos de escala de máquina virtual com PowerShell Azure
description: Cmdlets De PowerShell De Potência De Potência Azul Comuns para gerir conjuntos de escala de máquina virtual, tais como como iniciar e parar uma instância, ou alterar a capacidade definida de escala.
author: cynthn
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: cc83c8b73c73b2aa70bc36bad175e5c19c1ab700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275704"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Gerir um conjunto de escala de máquina virtual com O PowerShell Azure

Ao longo do ciclo de vida dos conjuntos de dimensionamento de máquinas virtuais, poderá ter de executar uma ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizam várias tarefas do ciclo de vida. Este artigo detalha algumas das cmdlets comuns da Azure PowerShell que lhe permitem executar estas tarefas.

Se precisar de criar um conjunto de escala de máquina virtual, pode [criar um conjunto de escala com o Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Ver informações sobre um conjunto de escala
Para ver a informação geral sobre um conjunto de escala, utilize [get-AzVmss](/powershell/module/az.compute/get-azvmss). O exemplo seguinte obtém informações sobre o conjunto de escala sem nome *myScaleSet* no grupo de recursos *myResourceGroup.* Insira os seus próprios nomes da seguinte forma:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Ver VMs num conjunto de dimensionamento
Para ver uma lista de vm num conjunto de escala, utilize [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). O exemplo seguinte lista todas as instâncias vM no conjunto de escala sem nome *myScaleSet* e no grupo de recursos *myResourceGroup.* Forneça os seus próprios valores para estes nomes:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Para ver informações adicionais sobre uma `-InstanceId` instância vm específica, adicione o parâmetro ao [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) e especifique uma instância para visualizar. O exemplo seguinte vê informações sobre a instância *VM 0* no conjunto de escala sem nome *myScaleSet* e o grupo de recursos *myResourceGroup.* Insira os seus próprios nomes da seguinte forma:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostraram informações sobre o seu conjunto de escala e os casos vm. Para aumentar ou diminuir o número de ocorrências no conjunto de escala, pode alterar a capacidade. O conjunto de escala cria ou remove automaticamente o número necessário de VMs, em seguida, configura os VMs para receber o tráfego de aplicações.

Primeiro, crie um objeto conjunto de escala com [Get-AzVmss,](/powershell/module/az.compute/get-azvmss)em seguida, especifique um novo valor para `sku.capacity`. Para aplicar a alteração de capacidade, utilize [a Actualização-AzVmss](/powershell/module/az.compute/update-azvmss). O exemplo seguinte atualiza o *myScaleSet* no grupo de recursos *myResourceGroup* para uma capacidade de *5* instâncias. Forneça os seus próprios valores da seguinte forma:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

São necessários alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Se diminuir a capacidade de um conjunto de escala, os VMs com os IDs de maior instância são removidos primeiro.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stop and start VMs in a scale set (Parar e iniciar VMs num conjunto de dimensionamento)
Para parar um ou mais VMs num conjunto de escala, utilize [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser paradas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são paradas. Para parar vários VMs, separe cada id de instância com uma vírem.

O exemplo seguinte para a instância *0* no conjunto de escala sem nome *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Por predefinição, as VMs paradas são desalocadas e não implicam custos de computação. Se quiser que a VM permaneça no estado aprovisionado quando for parada, adicione o parâmetro `-StayProvisioned` ao comando anterior. As VMs paradas que continuem aprovisionadas incorrem em custos de computação regulares.


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs em um conjunto de escala
Para iniciar um ou mais VMs num conjunto de escala, utilize [start-AzVmss](/powershell/module/az.compute/start-azvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser iniciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. Para iniciar vários VMs, separe cada id de cada instância com uma vírem.

O exemplo seguinte começa a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Restart VMs in a scale set (Reiniciar VMs num conjunto de dimensionamento)
Para reiniciar um ou mais VMs num conjunto de escala, utilize [o Restart-AzVmss](/powershell/module/az.compute/restart-azvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser reinciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. Para reiniciar vários VMs, separe cada id de instância com uma vírem.

O exemplo seguinte reinicia a instância *0* no conjunto de escala sem nome *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Remover VMs de um conjunto de escala
Para remover um ou mais VMs num conjunto de escala, utilize [remove-AzVmss](/powershell/module/az.compute/remove-azvmss). O `-InstanceId` parâmetro permite especificar um ou mais VMs para remover. Se não especificar um ID de instância, todos os VMs no conjunto de escala são removidos. Para remover vários VMs, separe cada id de instância com uma vírem.

O exemplo seguinte remove a instância *0* no conjunto de escala sem nome *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Passos seguintes
Outras tarefas comuns para conjuntos de escala incluem como [implementar uma aplicação](virtual-machine-scale-sets-deploy-app.md)e [atualizar instâncias vm](virtual-machine-scale-sets-upgrade-scale-set.md). Também pode utilizar o Azure PowerShell para [configurar regras de escala automática.](virtual-machine-scale-sets-autoscale-overview.md)
