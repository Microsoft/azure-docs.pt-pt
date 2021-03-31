---
title: Gerir conjuntos de balança de máquina virtual com Azure PowerShell
description: Cmdlets Common Azure PowerShell para gerir conjuntos de escala de máquina virtual, tais como como iniciar e parar uma instância, ou alterar a capacidade definida da escala.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: eee4dd7fae872f6b3ddd01f60aba732edc170766
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91570578"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Gerir uma balança de máquina virtual definida com Azure PowerShell

Ao longo do ciclo de vida dos conjuntos de dimensionamento de máquinas virtuais, poderá ter de executar uma ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizam várias tarefas do ciclo de vida. Este artigo detalha alguns dos cmdlets Azure PowerShell comuns que lhe permitem executar estas tarefas.

Se precisar de criar um conjunto de escala de máquina virtual, pode [criar um conjunto de escala com Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Ver informações sobre um conjunto de escala
Para ver a informação geral sobre um conjunto de escala, utilize [o Get-AzVmss](/powershell/module/az.compute/get-azvmss). O exemplo a seguir obtém informações sobre o conjunto de escala nomeado *myScaleSet* no grupo de recursos *myResourceGroup.* Insira os seus próprios nomes da seguinte forma:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Ver VMs num conjunto de dimensionamento
Para visualizar uma lista de instâncias VM num conjunto de escala, utilize [o Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). O exemplo a seguir lista todas as instâncias VM no conjunto de escala denominado *myScaleSet* e no grupo de recursos *myResourceGroup.* Forneça os seus próprios valores para estes nomes:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Para visualizar informações adicionais sobre uma instância VM específica, adicione o `-InstanceId` parâmetro à [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) e especifique um caso para visualizar. O exemplo a seguir visualiza informações sobre a instância *VM 0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Insira os seus próprios nomes da seguinte forma:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Também pode obter informações detalhadas *de exemploVer* informações para todas as instâncias numa chamada da API, o que pode ajudar a evitar o estrangulamento da API para grandes instalações.

```powershell
Get-AzVmssVM -InstanceView -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostraram informações sobre o seu conjunto de escalas e as instâncias VM. Para aumentar ou diminuir o número de casos na escala definida, pode alterar a capacidade. O conjunto de escala cria ou remove automaticamente o número necessário de VMs e, em seguida, configura os VMs para receber tráfego de aplicação.

Primeiro, crie um objeto definido em escala com [Get-AzVmss,](/powershell/module/az.compute/get-azvmss)em seguida, especifique um novo valor para `sku.capacity` . Para aplicar a alteração de capacidade, utilize [Update-AzVmss](/powershell/module/az.compute/update-azvmss). O exemplo a seguir atualiza *o myScaleSet* no grupo de recursos *myResourceGroup* para uma capacidade de *5* instâncias. Forneça os seus próprios valores da seguinte forma:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

São necessários alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Se diminuir a capacidade de um conjunto de escala, os VMs com os IDs de instância mais elevados são removidos primeiro.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stop and start VMs in a scale set (Parar e iniciar VMs num conjunto de dimensionamento)
Para parar um ou mais VMs num conjunto de escala, utilize [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser paradas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são paradas. Para parar vários VMs, separe cada identificação de instância com uma vírgula.

O exemplo a seguir para a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Por predefinição, as VMs paradas são desalocadas e não implicam custos de computação. Se quiser que a VM permaneça no estado aprovisionado quando for parada, adicione o parâmetro `-StayProvisioned` ao comando anterior. As VMs paradas que continuem aprovisionadas incorrem em custos de computação regulares.


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs em um conjunto de escala
Para iniciar um ou mais VMs num conjunto de escala, utilize [Start-AzVmss](/powershell/module/az.compute/start-azvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser iniciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. Para iniciar vários VMs, separe cada identificação de instância com uma vírgula.

O exemplo a seguir começa a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Restart VMs in a scale set (Reiniciar VMs num conjunto de dimensionamento)
Para reiniciar um ou mais VMs num conjunto de escala, utilize [Restart-AzVmss](/powershell/module/az.compute/restart-azvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser reinciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. Para reiniciar vários VMs, separe cada identificação de instância com uma vírgula.

O exemplo seguinte reinicia a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Remova VMs de um conjunto de escala
Para remover um ou mais VMs num conjunto de escala, utilize [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss). O `-InstanceId` parâmetro permite especificar um ou mais VMs para remover. Se não especificar um ID de instância, todos os VMs do conjunto de escala são removidos. Para remover vários VMs, separe cada identificação de instância com uma vírgula.

O exemplo seguinte remove a instância *0* no conjunto de escala denominado *myScaleSet* e o grupo de recursos *myResourceGroup.* Forneça os seus próprios valores da seguinte forma:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Passos seguintes
Outras tarefas comuns para conjuntos de escala incluem como [implementar uma aplicação,](virtual-machine-scale-sets-deploy-app.md)e [atualizar instâncias VM](virtual-machine-scale-sets-upgrade-scale-set.md). Também pode utilizar o Azure PowerShell para [configurar regras de escala automática](virtual-machine-scale-sets-autoscale-overview.md).
