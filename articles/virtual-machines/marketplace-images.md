---
title: Especifique informações do plano de compra do Mercado usando a Azure PowerShell
description: Saiba como especificar os detalhes do plano de compra do Azure Marketplace ao criar imagens numa Galeria de Imagens Partilhadas.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3de79e5cb3db2d0c52d13826900ec7160271edf9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86225163"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Fornecer informações sobre o plano de compra do Azure Marketplace ao criar imagens

Se estiver a criar uma imagem numa galeria partilhada, utilizando uma fonte originalmente criada a partir de uma imagem do Azure Marketplace, poderá ter de acompanhar as informações do plano de compra. Este artigo mostra como encontrar informações do plano de compra para um VM, em seguida, usar essa informação ao criar uma definição de imagem. Também cobrimos a utilização da informação a partir da definição de imagem para simplificar o fornecimento de informações do plano de compra ao criar um VM para uma imagem.

Para obter mais informações sobre encontrar e utilizar imagens do Marketplace, consulte [localizar e utilizar imagens do Azure Marketplace.](./windows/cli-ps-findimage.md)


## <a name="get-the-source-vm-information"></a>Obtenha a informação VM de origem
Se ainda tiver o VM original, pode obter o plano, editor e sku informações dele usando o Get-AzVM. Este exemplo obtém um VM nomeado *myVM* no grupo de recursos *myResourceGroup* e, em seguida, exibe as informações do plano de compra.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan.Publisher
$vm.Plan.Name
$vm.Plan.Product
```

## <a name="create-the-image-definition"></a>Criar a definição de imagem

Obtenha a galeria de imagens que pretende usar para armazenar a imagem. Primeiro, pode listar todas as galerias.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Em seguida, crie variáveis para a galeria que pretende utilizar. Neste exemplo, estamos a criar uma variável nomeada `$gallery` para *a myGallery* no grupo de recursos *myGalleryRG.*

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Crie a definição de imagem, utilizando o  `-PurchasePlanPublisher` `-PurchasePlanProduct` , e os `-PurchasePlanName` parâmetros.

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Em seguida, crie a sua versão de imagem utilizando [a Versão Nova-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). Pode criar uma versão de imagem a partir de um [VM,](image-version-vm-powershell.md#create-an-image-version) [imagem gerida,](image-version-managed-image-powershell.md#create-an-image-version) [VHD\snapshot,](image-version-snapshot-powershell.md#create-an-image-version)ou [outra versão de imagem](image-version-another-gallery-powershell.md#create-the-image-version). 


## <a name="create-the-vm"></a>Criar a VM

Quando for criar um VM a partir da imagem, pode utilizar as informações a partir da definição de imagem para passar nas informações do editor utilizando o [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan).


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre encontrar e utilizar imagens do Marketplace, consulte [localizar e utilizar imagens do Azure Marketplace.](./windows/cli-ps-findimage.md)