---
title: Mova um VM na utilização do CLI Azure
description: Mover um VM para outro grupo de subscrição ou recursos Azure utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 7dbe06a9f2fff8abf59adbdfc9e41055c85e8f2c
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889302"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Mover um VM para outro grupo de subscrição ou recursos
Este artigo explica-lhe como mover uma máquina virtual (VM) entre grupos de recursos ou subscrições. Mover um VM entre subscrições pode ser útil se criar um VM numa subscrição pessoal e agora quiser movê-lo para a subscrição da sua empresa.

> [!IMPORTANT]
>Novos IDs de recursos são criados como parte do movimento. Depois de o VM ter sido movido, terá de atualizar as suas ferramentas e scripts para utilizar os novos IDs de recursos.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Use o CLI Azure para mover um VM


Antes de poder mover o seu VM utilizando o CLI Azure, tem de se certificar de que as assinaturas de origem e destino existem dentro do mesmo inquilino. Para verificar se ambas as subscrições têm o mesmo ID do inquilino, use [a az conta show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Se as identificações do arrendatário para as assinaturas de origem e destino não forem as mesmas, deve contactar o [apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) para transferir os recursos para um novo inquilino.

Para mover com sucesso um VM, é necessário mover o VM e todos os seus recursos de apoio. Utilize o comando [da lista de recursos az](/cli/azure/resource) para listar todos os recursos de um grupo de recursos e seus IDs. Ajuda a canalizar a saída deste comando para um ficheiro para que possa copiar e colar os IDs em comandos posteriores.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```
A `table` saída não está disponível se `--interactive` utilizar. Altere a saída para outra opção como `json` .

Para mover um VM e os seus recursos para outro grupo de recursos, utilize [o movimento de recursos az](/cli/azure/resource). O exemplo a seguir mostra como mover um VM e os recursos mais comuns que necessita. Use o parâmetro **de ids** e passe numa lista separada por vírgula (sem espaços) de IDs para que os recursos se movam.

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

Se quiser mover o VM e os seus recursos para uma subscrição diferente, adicione o parâmetro **--subscrição de destinoDida** para especificar a subscrição do destino.

Quando lhe for pedido que confirme que pretende mover os recursos especificados, insira **Y** para confirmar.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Passos seguintes
Você pode mover vários tipos diferentes de recursos entre grupos de recursos e subscrições. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)    
