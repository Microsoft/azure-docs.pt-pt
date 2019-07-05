---
title: Mover um recurso de VM do Windows no Azure | Documentos da Microsoft
description: Mova uma VM do Windows para outro Azure subscrição ou grupo de recursos no modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: d67f9e9ad99ccd6267de9dbb9fd0c64adb41f0cb
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565563"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Mover uma VM do Windows para outro grupo de subscrição ou ao recurso do Azure
Este artigo explica como mover uma máquina virtual de Windows (VM) entre grupos de recursos ou subscrições. A movimentação entre subscrições, pode ser útil se tiver criado originalmente uma VM numa subscrição de pessoal e agora quero movê-lo para a subscrição da sua empresa para continuar o seu trabalho. Não é necessário iniciar a VM para movê-lo e deverá continuar a executar durante a mudança.

> [!IMPORTANT]
>Novos IDs de recurso são criados como parte da transição. Depois da VM foi movida, terá de atualizar as suas ferramentas e scripts para usar os novos IDs de recurso. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Utilize o Powershell para mover uma VM

Para mover uma máquina virtual para outro grupo de recursos, terá de certificar-se de que também mova todos os recursos dependentes. Para obter uma lista com o ID de recurso de cada um destes recursos, utilize o [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet.

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

Pode utilizar a saída do comando anterior como uma lista separada por vírgulas de IDs de recurso para [movimentação AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) para mover cada recurso para o destino. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Para mover os recursos para uma subscrição diferente, inclua o **- DestinationSubscriptionId** parâmetro. 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Quando lhe for pedido que confirme que pretende mover os recursos especificados, introduza **Y** para confirmar.

## <a name="next-steps"></a>Passos Seguintes
Pode mover vários tipos de recursos entre grupos de recursos e subscrições. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou subscrição](../../resource-group-move-resources.md).    

