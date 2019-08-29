---
title: Usar grupos de posicionamento de proximidade para VMs do Windows | Microsoft Docs
description: Saiba mais sobre como criar e usar grupos de posicionamento de proximidade para máquinas virtuais do Windows no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: af75b3f98232d6507fc8b0fda179bebc75828086
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088826"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-powershell"></a>Pré-visualização: Implantar VMs em grupos de posicionamento de proximidade usando o PowerShell


Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um [grupo de posicionamento de proximidade](co-location.md#preview-proximity-placement-groups).

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.

> [!IMPORTANT]
> Os grupos de posicionamento de proximidade estão atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Os grupos de posicionamento de proximidade não estão disponíveis nessas regiões durante a visualização: **Leste do Japão**, **leste da Austrália** e **Índia central**.


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

Crie uma VM no grupo de posicionamento de proximidade `-ProximityPlacementGroup $ppg.Id` usando para se referir à ID do grupo de posicionamento de proximidade ao usar [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) para criar a VM.

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
Você também pode criar um conjunto de disponibilidade em seu grupo de posicionamento de proximidade. Use o mesmo `-ProximityPlacementGroup` parâmetro com o cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) para criar um conjunto de disponibilidade e todas as VMs criadas no conjunto de disponibilidade também serão criadas no mesmo grupo de posicionamento de proximidade.

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Você também pode criar um conjunto de dimensionamento em seu grupo de posicionamento de proximidade. Use o mesmo `-ProximityPlacementGroup` parâmetro com [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) para criar um conjunto de dimensionamento e todas as instâncias serão criadas no mesmo grupo de posicionamento de proximidade.

## <a name="next-steps"></a>Passos seguintes

Você também pode usar o [CLI do Azure](../linux/proximity-placement-groups.md) para criar grupos de posicionamento de proximidade.
