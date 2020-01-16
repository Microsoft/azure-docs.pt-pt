---
title: Mover uma VM do Linux no Azure
description: Mova uma VM do Linux para outra assinatura do Azure ou grupo de recursos no modelo de implantação do Resource Manager.
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
ms.openlocfilehash: 2ba8a8cdc324f46e25f9665cfce0aa07fc948e88
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979027"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Mover uma VM do Linux para outra assinatura ou grupo de recursos
Este artigo explica como mover uma VM (máquina virtual) do Linux entre grupos de recursos ou assinaturas. Mover uma VM entre assinaturas pode ser útil se você criou uma VM em uma assinatura pessoal e agora deseja movê-la para a assinatura da sua empresa.

> [!IMPORTANT]
>Não é possível mover o Azure Managed Disks neste momento.
>
>Novas IDs de recurso são criadas como parte da movimentação. Depois que a VM for movida, você precisará atualizar suas ferramentas e scripts para usar as novas IDs de recurso.
>
>

## <a name="use-the-azure-cli-to-move-a-vm"></a>Usar o CLI do Azure para mover uma VM


Antes de poder mover sua VM usando o CLI do Azure, você precisa verificar se as assinaturas de origem e destino existem no mesmo locatário. Para verificar se ambas as assinaturas têm a mesma ID de locatário, use [AZ Account show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Se as IDs de locatário para as assinaturas de origem e de destino não forem iguais, você deverá entrar em contato com o [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) para mover os recursos para um novo locatário.

Para mover uma VM com êxito, você precisa mover a VM e todos os seus recursos de suporte. Use o comando [AZ Resource List](/cli/azure/resource) para listar todos os recursos em um grupo de recursos e suas IDs. Ele ajuda a direcionar a saída desse comando para um arquivo para que você possa copiar e colar as IDs em comandos posteriores.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Para mover uma VM e seus recursos para outro grupo de recursos, use [AZ Resource move](/cli/azure/resource). O exemplo a seguir mostra como mover uma VM e os recursos mais comuns necessários. Use o parâmetro **-IDs** e passe uma lista separada por vírgulas (sem espaços) de IDs para os recursos a serem movidos.

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

Se você quiser mover a VM e seus recursos para uma assinatura diferente, adicione o parâmetro **--Destination-SubscriptionId** para especificar a assinatura de destino.

Quando for solicitado que você confirme que deseja mover os recursos especificados, digite **Y** para confirmar.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Passos seguintes
Você pode mover muitos tipos diferentes de recursos entre grupos de recursos e assinaturas. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
