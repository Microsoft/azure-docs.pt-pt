---
title: Mova um recurso VM windows em Azure
description: Mova um VM do Windows para outro grupo de subscrição ou recursos Azure no modelo de implementação do Gestor de Recursos.
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
ms.openlocfilehash: ed29c92d20a6b0d749ec44a22f42ec446ec58650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919571"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Mover uma VM do Windows para outra subscrição ou grupo de recursos do Azure
Este artigo explica-o como mover uma máquina virtual do Windows (VM) entre grupos de recursos ou subscrições. Mover-se entre subscrições pode ser útil se criou originalmente um VM numa subscrição pessoal e agora quer movê-lo para a subscrição da sua empresa para continuar o seu trabalho. Não é necessário iniciar o VM para o mover e deve continuar a funcionar durante a mudança.

> [!IMPORTANT]
>Novos IDs de recursos são criados como parte do movimento. Depois de movido o VM, terá de atualizar as suas ferramentas e scripts para utilizar os novos IDs de recursos.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Use powershell para mover um VM

Para mover uma máquina virtual para outro grupo de recursos, você precisa ter certeza de que também move todos os recursos dependentes. Para obter uma lista com a identificação de recursos de cada um destes recursos, use o cmdlet [Get-AzResource.](https://docs.microsoft.com/powershell/module/az.resources/get-azresource)

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Pode utilizar a saída do comando anterior para criar uma lista separada de IDs de recursos para [move-azResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) para mover cada recurso para o destino.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Para mover os recursos para diferentes subscrições, inclua o parâmetro **-DestinationSubscriptionId.**

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Quando lhe for pedido que confirme que pretende mover os recursos especificados, insira **Y** para confirmar.

## <a name="next-steps"></a>Passos seguintes
Você pode mover muitos tipos diferentes de recursos entre grupos de recursos e subscrições. Para mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)    
