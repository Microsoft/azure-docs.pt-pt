---
title: Usar grupos de posicionamento de proximidade para VMs do Windows
description: Saiba mais sobre como criar e usar grupos de posicionamento de proximidade para máquinas virtuais do Windows no Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d0c35737151b060dcffba8944f4a1361d36dc14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171217"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Implantar VMs em grupos de posicionamento de proximidade usando o PowerShell


Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um [grupo de posicionamento de proximidade](co-location.md#proximity-placement-groups).

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


## <a name="create-a-proximity-placement-group"></a>Criar um grupo de colocação em proximidade
Crie um grupo de posicionamento de proximidade usando o cmdlet [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) . 

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

Você pode listar todos os grupos de posicionamento de proximidade usando o cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Criar uma VM

Crie uma VM no grupo de posicionamento de proximidade usando `-ProximityPlacementGroup $ppg.Id` para se referir à ID do grupo de posicionamento de proximidade ao usar [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) para criar a VM.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Você pode ver a VM no grupo de posicionamento usando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>Conjuntos de Disponibilidade
Você também pode criar um conjunto de disponibilidade em seu grupo de posicionamento de proximidade. Use o mesmo parâmetro `-ProximityPlacementGroup` com o cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) para criar um conjunto de disponibilidade e todas as VMs criadas no conjunto de disponibilidade também serão criadas no mesmo grupo de posicionamento de proximidade.

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Você também pode criar um conjunto de dimensionamento em seu grupo de posicionamento de proximidade. Use o mesmo parâmetro `-ProximityPlacementGroup` com [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) para criar um conjunto de dimensionamento e todas as instâncias serão criadas no mesmo grupo de posicionamento de proximidade.

## <a name="next-steps"></a>Passos seguintes

Você também pode usar o [CLI do Azure](../linux/proximity-placement-groups.md) para criar grupos de posicionamento de proximidade.
