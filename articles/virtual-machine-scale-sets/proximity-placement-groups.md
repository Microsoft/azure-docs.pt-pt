---
title: Pré-visualização de grupos de colocação de proximidade para conjuntos de escala de máquinas virtuais
description: Aprenda a criar e utilizar grupos de colocação de proximidade para conjuntos de escala de máquinas virtuais windows em Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273624"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Pré-visualização: Criar e utilizar grupos de colocação de proximidade usando powerShell

Para obter VMs o mais próximo possível, alcançando a latência mais baixa possível, você deve colocar a sua escala definida dentro de um [grupo de colocação de proximidade](co-location.md#preview-proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos da computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.

> [!IMPORTANT]
> Os Grupos de Colocação de Proximidade estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
>
> Os grupos de colocação de proximidade não estão disponíveis nestas regiões durante a pré-visualização: **Japão Leste,** **Austrália Leste** e Índia **Central**.


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

## <a name="list-proximity-placement-groups"></a>Lista de grupos de colocação de proximidade

Pode listar todos os grupos de colocação de proximidade utilizando o cmdlet [Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento

Crie uma escala no grupo `-ProximityPlacementGroup $ppg.Id` de colocação de proximidade usando para se referir ao ID do grupo de colocação de proximidade quando você usar [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) para criar o conjunto de escala.

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

Pode ver a ocorrência no grupo de colocação utilizando [o Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Passos seguintes

Também pode utilizar o [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) para criar grupos de colocação de proximidade.
