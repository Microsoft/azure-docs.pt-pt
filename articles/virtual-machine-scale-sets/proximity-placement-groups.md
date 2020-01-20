---
title: Visualização de grupos de posicionamento de proximidade para conjuntos de dimensionamento de máquinas virtuais
description: Saiba mais sobre como criar e usar grupos de posicionamento de proximidade para conjuntos de dimensionamento de máquinas virtuais do Windows no Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273624"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Versão prévia: Criando e usando grupos de posicionamento de proximidade usando o PowerShell

Para obter as VMs o mais próximo possível, obtendo a menor latência possível, você deve implantar seu conjunto de dimensionamento em um [grupo de posicionamento de proximidade](co-location.md#preview-proximity-placement-groups).

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.

> [!IMPORTANT]
> Os grupos de posicionamento de proximidade estão atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Os grupos de posicionamento de proximidade não estão disponíveis nessas regiões durante a versão prévia: **leste do Japão**, **leste da Austrália** e **Índia central**.


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


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento

Crie uma escala no grupo de posicionamento de proximidade usando `-ProximityPlacementGroup $ppg.Id` para se referir à ID do grupo de posicionamento de proximidade ao usar [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) para criar o conjunto de dimensionamento.

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

Você pode ver a instância no grupo de posicionamento usando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Passos seguintes

Você também pode usar o [CLI do Azure](../virtual-machines/linux/proximity-placement-groups.md) para criar grupos de posicionamento de proximidade.
