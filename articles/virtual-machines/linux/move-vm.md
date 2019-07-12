---
title: Mover uma VM do Linux no Azure | Documentos da Microsoft
description: Mova uma VM do Linux para outro Azure subscrição ou grupo de recursos no modelo de implementação do Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 7c22fe8beea894bccb311a63a1be70c972188e59
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667291"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Mover uma VM do Linux para outra subscrição ou grupo de recursos
Este artigo explica como mover uma máquina virtual (VM) do Linux entre grupos de recursos ou subscrições. Mover uma VM entre subscrições, pode ser útil se tiver criado uma VM numa subscrição de pessoal e agora quero movê-lo para a subscrição da sua empresa.

> [!IMPORTANT]
>Não é possível mover o Managed Disks do Azure neste momento. 
>
>Novos IDs de recurso são criados como parte da transição. Depois da VM foi movida, terá de atualizar as suas ferramentas e scripts para usar os novos IDs de recurso. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Utilizar a CLI do Azure para mover uma VM


Antes de prosseguir a VM com a CLI do Azure, terá de certificar-se de que as subscrições de origem e de destino existem no mesmo inquilino. Para verificar que ambas as subscrições têm o mesmo ID de inquilino, utilize [show de conta de az](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Se os IDs de inquilino para as subscrições de origem e de destino não são os mesmos, tem de contactar [suportar](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) para mover os recursos para um novo inquilino.

Para mover com êxito uma VM, terá de mover a VM e todos os seus recursos de suporte. Utilize o [lista de recursos de az](/cli/azure/resource) command para listar todos os recursos num grupo de recursos e os respetivos IDs. Ele ajuda a encaminhar o resultado deste comando num arquivo para que possa copiar e colar os IDs de comandos posteriores.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Para mover uma VM e os respetivos recursos para outro grupo de recursos, utilize [movimentação do recurso de az](/cli/azure/resource). O exemplo seguinte mostra como mover uma VM e dos recursos mais comuns que precisa. Utilize o **-ids** parâmetro e passe uma lista separada por vírgulas (sem espaços) de IDs para os recursos para mover.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Se pretender mover a VM e os respetivos recursos para uma subscrição diferente, adicione a **– destino-subscriptionId** parâmetro para especificar a subscrição de destino.

Quando lhe for pedido que confirme que pretende mover os recursos especificados, introduza **Y** para confirmar.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Passos Seguintes
Pode mover vários tipos de recursos entre grupos de recursos e subscrições. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou subscrição](../../resource-group-move-resources.md).    

