---
title: 'PowerShell: Utilize grupos de colocação de proximidade'
description: Aprenda sobre a criação e utilização de grupos de colocação de proximidade usando o Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: c1c144ac9db040bfac45ecc7838401ae09c9e2c4
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137994"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Implementar VMs para grupos de colocação de proximidade usando powerShell


Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um grupo de [colocação de proximidade](co-location.md#proximity-placement-groups).

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


## <a name="create-a-proximity-placement-group"></a>Criar um grupo de colocação em proximidade
Crie um grupo de colocação de proximidade utilizando o cmdlet [New-AzCloseyPlacementGroup.](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Listar grupos de posicionamento de proximidade

Pode listar todos os grupos de colocação de proximidade utilizando o cmdlet [Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Criar uma VM

Crie um VM no grupo de colocação de proximidade usando `-ProximityPlacementGroup $ppg.Id` para se referir ao ID do grupo de colocação de proximidade quando utilizar o [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) para criar o VM.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Pode ver o VM no grupo de colocação utilizando [o Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Mover um VM existente para um grupo de colocação de proximidade

Você também pode adicionar um VM existente a um grupo de colocação de proximidade. Tem de parar o VM primeiro, depois atualizar o VM e reiniciar.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Restart-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Mova um VM existente para fora de um grupo de colocação de proximidade

Para remover um VM de um grupo de colocação de proximidade, é necessário parar o VM primeiro, em seguida, atualizar o VM e reiniciar.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroupId = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Restart-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Conjuntos de Disponibilidade
Também pode criar um conjunto de disponibilidade sintetária no seu grupo de colocação de proximidade. Utilize o mesmo parâmetro de `-ProximityPlacementGroup` com o [Cmdlet New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) para criar um conjunto de disponibilidade e todos os VMs criados no conjunto de disponibilidade também serão criados no mesmo grupo de colocação de proximidade.

Para adicionar ou remover um conjunto de disponibilidade existente para um grupo de colocação de proximidade, você primeiro precisa parar todos os VMs no conjunto de disponibilidade. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Mova uma disponibilidade existente definida para um grupo de colocação de proximidade

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Mover uma disponibilidade existente definida para fora de um grupo de colocação de proximidade

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Também pode criar um conjunto de escala no seu grupo de colocação de proximidade. Utilize o mesmo parâmetro de `-ProximityPlacementGroup` com [new-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) para criar um conjunto de escala e todas as instâncias serão criadas no mesmo grupo de colocação de proximidade.


Para adicionar ou remover uma escala existente definida para um grupo de colocação de proximidade, primeiro precisa parar o conjunto de escala. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Mover uma escala existente definida para um grupo de colocação de proximidade

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Restart-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Mover uma escala existente definida para fora de um grupo de colocação de proximidade

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Restart-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Também pode utilizar o [Azure CLI](../linux/proximity-placement-groups.md) para criar grupos de colocação de proximidade.
