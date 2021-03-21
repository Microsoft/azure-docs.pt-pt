---
title: Partilhe imagens de galeria entre inquilinos em Azure
description: Saiba como partilhar imagens VM através de inquilinos da Azure usando Galerias de Imagem Partilhada e PowerShell.
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/15/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: e78cb29551bf7eb3f713edb755464eb7696106e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556133"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-powershell"></a>Partilhar imagens da galeria VM através dos inquilinos do Azure usando o PowerShell

As Galerias de Imagem Partilhadas permitem-lhe partilhar imagens usando o Azure RBAC. Você pode usar a Azure RBAC para compartilhar imagens dentro do seu inquilino, e até mesmo para indivíduos fora do seu inquilino. Para mais informações sobre esta simples opção de partilha, consulte a [galeria Partilhar.](./shared-images-portal.md#share-the-gallery)

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Não é possível utilizar o portal para implantar um VM a partir de uma imagem em outro inquilino azul. Para criar um VM a partir de uma imagem partilhada entre inquilinos, você deve usar o [Azure CLI](../linux/share-images-across-tenants.md) ou Powershell.

## <a name="create-a-vm-using-powershell"></a>Criar uma VM com o PowerShell

Inicie sessão em ambos os inquilinos usando a identificação do formulário, identificação secreta e inquilino. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Crie o VM no grupo de recursos que tenha permissão no registo da aplicação. Substitua as informações neste exemplo por as suas.



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Set a variable for the image version in Tenant 1 using the full image ID of the shared image version
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Networking pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using the $image variable to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $image | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="next-steps"></a>Passos seguintes

Também pode criar recursos de galeria de imagens partilhadas utilizando o [portal Azure.](shared-images-portal.md)
