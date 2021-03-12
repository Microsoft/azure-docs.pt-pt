---
title: Mover um recurso VM do Windows em Azure
description: Mover um VM do Windows para outro grupo de subscrição ou recursos Azure no modelo de implementação do Gestor de Recursos.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: c5d3445144178ce855a5ce904b42d6e68a8e7d29
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555249"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Mover uma VM do Windows para outra subscrição ou grupo de recursos do Azure
Este artigo explica-lhe como mover uma máquina virtual Do Windows (VM) entre grupos de recursos ou subscrições. Mover-se entre subscrições pode ser útil se originalmente criou um VM numa subscrição pessoal e agora quer movê-lo para a subscrição da sua empresa para continuar o seu trabalho. Não é necessário parar o VM para movê-lo e deve continuar a funcionar durante a mudança.

> [!IMPORTANT]
>Novos IDs de recursos são criados como parte do movimento. Depois de o VM ter sido movido, terá de atualizar as suas ferramentas e scripts para utilizar os novos IDs de recursos.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Use Powershell para mover um VM

Para mover uma máquina virtual para outro grupo de recursos, você precisa ter certeza de que você também move todos os recursos dependentes. Para obter uma lista com o ID de recursos de cada um destes recursos, use o [cmdlet Get-AzResource.](/powershell/module/az.resources/get-azresource)

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Pode utilizar a saída do comando anterior para criar uma lista separada por vírgula de IDs de recursos para [Move-AzResource](/powershell/module/az.resources/move-azresource) para mover cada recurso para o destino.

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
Você pode mover vários tipos diferentes de recursos entre grupos de recursos e subscrições. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)    
