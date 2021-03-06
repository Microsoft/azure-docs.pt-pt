---
title: Pré-visualização de grupos de colocação de proximidade para conjuntos de escala de máquina virtual
description: Saiba como criar e utilizar grupos de colocação de proximidade para conjuntos de escala de máquina virtual Windows em Azure.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: proximity-placement-groups
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: eeb0dd6cd5ce838c0f3a68763c40612038ee82f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933496"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Pré-visualização: Criar e utilizar grupos de colocação de proximidade utilizando o PowerShell

Para obter VMs o mais próximo possível, alcançando a menor latência possível, deve implementar a sua escala definida dentro de um [grupo de colocação de proximidade](../virtual-machines/co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.

> [!IMPORTANT]
> Grupos de Colocação de Proximidade estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Os grupos de colocação de proximidade não estão disponíveis nestas regiões durante a pré-visualização: **Japão Leste,** **Austrália Oriental** e **Índia Central.**


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


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento

Crie uma escala no grupo de colocação de proximidade utilizando `-ProximityPlacementGroup $ppg.Id` para se referir ao ID do grupo de colocação de proximidade quando utilizar o [New-AzVMSS](/powershell/module/az.compute/new-azvmss) para criar o conjunto de escala.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Pode ver a instância no grupo de colocação usando o [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Passos seguintes

Também pode usar o [CLI Azure](../virtual-machines/linux/proximity-placement-groups.md) para criar grupos de colocação de proximidade.
