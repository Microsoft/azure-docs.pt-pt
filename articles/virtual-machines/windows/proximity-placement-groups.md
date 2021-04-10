---
title: Criar um grupo de colocação de proximidade usando a Azure PowerShell
description: Saiba como criar e utilizar grupos de colocação de proximidade usando a Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 26921b3d102032cb36f47c3be7a79c2b596a1d0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503679"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-powershell"></a>Implementar VMs para grupos de colocação de proximidade usando Azure PowerShell


Para obter VMs o mais próximo possível, alcançando a menor latência possível, deve implantá-los dentro de um [grupo de colocação de proximidade](../co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


## <a name="create-a-proximity-placement-group"></a>Criar um grupo de colocação em proximidade
Crie um grupo de colocação de proximidade utilizando o [cmdlet New-AzProximityPlacementGroup.](/powershell/module/az.compute/new-azproximityplacementgroup) 

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

## <a name="list-proximity-placement-groups"></a>Listar grupos de colocação de proximidade

Pode listar todos os grupos de colocação de proximidade utilizando o cmdlet [Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Criar uma VM

Crie um VM no grupo de colocação de proximidade utilizando `-ProximityPlacementGroup $ppg.Id` para se referir ao ID do grupo de colocação de proximidade quando utilizar o [New-AzVM](/powershell/module/az.compute/new-azvm) para criar o VM.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Pode ver o VM no grupo de colocação utilizando o [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Mover um VM existente para um grupo de colocação de proximidade

Também pode adicionar um VM existente a um grupo de colocação de proximidade. Primeiro, tens de parar com o VM, depois atualizar o VM e reiniciar.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Mover um VM existente de um grupo de colocação de proximidade

Para remover um VM de um grupo de colocação de proximidade, é necessário parar primeiro o VM, depois atualizar o VM e reiniciar.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Conjuntos de Disponibilidade
Também pode criar um conjunto de disponibilidade no seu grupo de colocação de proximidade. Utilize o mesmo `-ProximityPlacementGroup` parâmetro com o cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) para criar um conjunto de disponibilidade e todos os VMs criados no conjunto de disponibilidade também serão criados no mesmo grupo de colocação de proximidade.

Para adicionar ou remover um conjunto de disponibilidade existente para um grupo de colocação de proximidade, você precisa primeiro parar todos os VMs no conjunto de disponibilidade. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Mover uma disponibilidade existente definida para um grupo de colocação de proximidade

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

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Mover uma disponibilidade existente definida de um grupo de colocação de proximidade

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

Também pode criar um conjunto de escala no seu grupo de colocação de proximidade. Use o mesmo `-ProximityPlacementGroup` parâmetro com [New-AzVmss](/powershell/module/az.compute/new-azvmss) para criar um conjunto de escala e todas as instâncias serão criadas no mesmo grupo de colocação de proximidade.


Para adicionar ou remover uma escala existente definida para um grupo de colocação de proximidade, primeiro precisa parar o conjunto de escala. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Mova uma escala existente definida para um grupo de colocação de proximidade

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Mova uma escala existente definida de um grupo de colocação de proximidade

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Também pode usar o [CLI Azure](../linux/proximity-placement-groups.md) para criar grupos de colocação de proximidade.