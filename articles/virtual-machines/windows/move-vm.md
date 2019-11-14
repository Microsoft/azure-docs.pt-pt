---
title: Mover um recurso de VM do Windows no Azure
description: Mova uma VM do Windows para outra assinatura do Azure ou grupo de recursos no modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 9264e36922cb88c541ba9fb2fe54a9606f371b72
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033184"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Mover uma VM do Windows para outra assinatura ou grupo de recursos do Azure
Este artigo explica como mover uma VM (máquina virtual) do Windows entre grupos de recursos ou assinaturas. A movimentação entre assinaturas pode ser útil se você originalmente criou uma VM em uma assinatura pessoal e agora deseja movê-la para a assinatura da sua empresa para continuar seu trabalho. Você não precisa iniciar a VM para movê-la e ela deve continuar a ser executada durante a movimentação.

> [!IMPORTANT]
>Novas IDs de recurso são criadas como parte da movimentação. Depois que a VM for movida, você precisará atualizar suas ferramentas e scripts para usar as novas IDs de recurso. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Usar o PowerShell para mover uma VM

Para mover uma máquina virtual para outro grupo de recursos, você precisa ter certeza de que também moverá todos os recursos dependentes. Para obter uma lista com a ID de recurso de cada um desses recursos, use o cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) .

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

Você pode usar a saída do comando anterior como uma lista separada por vírgulas de IDs de recurso para [mover-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) para mover cada recurso para o destino. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Para mover os recursos para uma assinatura diferente, inclua o parâmetro **-DestinationSubscriptionId** . 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Quando for solicitado que você confirme que deseja mover os recursos especificados, digite **Y** para confirmar.

## <a name="next-steps"></a>Passos seguintes
Você pode mover muitos tipos diferentes de recursos entre grupos de recursos e assinaturas. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou assinatura](../../resource-group-move-resources.md).    

