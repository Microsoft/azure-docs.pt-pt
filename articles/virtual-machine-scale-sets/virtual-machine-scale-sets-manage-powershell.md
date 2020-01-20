---
title: Gerenciar conjuntos de dimensionamento de máquinas virtuais com Azure PowerShell
description: Os cmdlets Azure PowerShell comuns para gerenciar conjuntos de dimensionamento de máquinas virtuais, como como iniciar e parar uma instância, ou alterar a capacidade do conjunto de dimensionamento.
author: cynthn
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: cc83c8b73c73b2aa70bc36bad175e5c19c1ab700
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275704"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Gerenciar um conjunto de dimensionamento de máquinas virtuais com Azure PowerShell

Ao longo do ciclo de vida dos conjuntos de dimensionamento de máquinas virtuais, poderá ter de executar uma ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizam várias tarefas do ciclo de vida. Este artigo detalha alguns dos cmdlets Azure PowerShell comuns que permitem executar essas tarefas.

Se você precisar criar um conjunto de dimensionamento de máquinas virtuais, poderá [criar um conjunto de dimensionamento com Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Exibir informações sobre um conjunto de dimensionamento
Para exibir as informações gerais sobre um conjunto de dimensionamento, use [Get-AzVmss](/powershell/module/az.compute/get-azvmss). O exemplo a seguir obtém informações sobre o conjunto de dimensionamento chamado *Myscalemodeset* no grupo de recursos *MyResource* Group. Insira seus próprios nomes da seguinte maneira:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Ver VMs num conjunto de dimensionamento
Para exibir uma lista de instâncias de VM em um conjunto de dimensionamento, use [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). O exemplo a seguir lista todas as instâncias de VM no conjunto de dimensionamento chamado *Myscalemodeset* e no grupo de recursos *MyResource* Group. Forneça seus próprios valores para esses nomes:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Para exibir informações adicionais sobre uma instância específica de VM, adicione o parâmetro `-InstanceId` para [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) e especifique uma instância para exibir. O exemplo a seguir exibe informações sobre a instância de VM *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Insira seus próprios nomes da seguinte maneira:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostraram informações sobre o conjunto de dimensionamento e as instâncias de VM. Para aumentar ou diminuir o número de instâncias no conjunto de dimensionamento, você pode alterar a capacidade. O conjunto de dimensionamento cria ou remove automaticamente o número necessário de VMs e, em seguida, configura as VMs para receber o tráfego do aplicativo.

Primeiro, crie um objeto de conjunto de dimensionamento com [Get-AzVmss](/powershell/module/az.compute/get-azvmss)e especifique um novo valor para `sku.capacity`. Para aplicar a alteração de capacidade, use [Update-AzVmss](/powershell/module/az.compute/update-azvmss). O exemplo a seguir atualiza *Myscalemodeset* no grupo de recursos *MyResource* Group para uma capacidade de *5* instâncias. Forneça seus próprios valores da seguinte maneira:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

São necessários alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Se você diminuir a capacidade de um conjunto de dimensionamento, as VMs com as IDs de instância mais alta serão removidas primeiro.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Parar e iniciar VMs em um conjunto de dimensionamento
Para interromper uma ou mais VMs em um conjunto de dimensionamento, use [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser paradas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são paradas. Para interromper várias VMs, separe cada ID de instância com uma vírgula.

O exemplo a seguir interrompe a instância *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Forneça seus próprios valores da seguinte maneira:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Por predefinição, as VMs paradas são desalocadas e não implicam custos de computação. Se quiser que a VM permaneça no estado aprovisionado quando for parada, adicione o parâmetro `-StayProvisioned` ao comando anterior. As VMs paradas que continuem aprovisionadas incorrem em custos de computação regulares.


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs em um conjunto de dimensionamento
Para iniciar uma ou mais VMs em um conjunto de dimensionamento, use [Start-AzVmss](/powershell/module/az.compute/start-azvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser iniciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. Para iniciar várias VMs, separe cada ID de instância com uma vírgula.

O exemplo a seguir inicia a instância *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Forneça seus próprios valores da seguinte maneira:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Reiniciar VMs em um conjunto de dimensionamento
Para reiniciar uma ou mais VMs em um conjunto de dimensionamento, use [Restart-AzVmss](/powershell/module/az.compute/restart-azvmss). O parâmetro `-InstanceId` permite-lhe especificar uma ou mais VMs que deverão ser reinciadas. Se não especificar um ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. Para reiniciar várias VMs, separe cada ID de instância com uma vírgula.

O exemplo a seguir reinicia a instância *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Forneça seus próprios valores da seguinte maneira:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Remover VMs de um conjunto de dimensionamento
Para remover uma ou mais VMs em um conjunto de dimensionamento, use [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs a serem removidas. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento serão removidas. Para remover várias VMs, separe cada ID de instância com uma vírgula.

O exemplo a seguir remove a instância *0* no conjunto de dimensionamento chamado *myscalemodeset* e o grupo de recursos *MyResource* Group. Forneça seus próprios valores da seguinte maneira:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Passos seguintes
Outras tarefas comuns para conjuntos de dimensionamento incluem como [implantar um aplicativo](virtual-machine-scale-sets-deploy-app.md)e [atualizar instâncias de VM](virtual-machine-scale-sets-upgrade-scale-set.md). Você também pode usar Azure PowerShell para [configurar regras de dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md).
