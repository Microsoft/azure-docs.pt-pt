---
title: Criar uma VM a partir de uma imagem generalizada
description: Crie um VM usando uma imagem generalizada numa Galeria de Imagem Partilhada.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: efce2eb33bb95a3dc13cec4cbe0ea89d1a2c3452
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796749"
---
# <a name="create-a-vm-using-a-generalized-image"></a>Criar um VM usando uma imagem generalizada 

Crie um VM a partir de uma imagem generalizada armazenada numa Galeria de Imagem Partilhada. Se quiser criar um VM utilizando uma imagem especializada, consulte [Criar um VM a partir de uma imagem especializada](vm-specialized-image-version-powershell.md).

Uma vez que tenha uma versão de imagem generalizada, pode criar um ou mais novos VMs. Utilizando o cmdlet [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 

Neste exemplo, estamos a usar o ID de definição de imagem para garantir que o seu novo VM utilizará a versão mais recente de uma imagem. Também pode utilizar uma versão específica utilizando `Set-AzVMSourceImage -Id`o ID da versão de imagem para . Por exemplo, utilizar a versão de imagem `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` *1.0.0* tipo: . 

Esteja ciente de que usar uma versão de imagem específica significa que a automatização pode falhar se essa versão de imagem específica não estiver disponível porque foi eliminada ou removida da região. Recomendamos a utilização do ID de definição de imagem para criar o seu novo VM, a menos que seja necessária uma versão de imagem específica.

Substitua os nomes de recursos conforme necessário nestes exemplos. 

## <a name="simplified-parameter-set"></a>Conjunto de parâmetrosimplificado

Pode utilizar o parâmetro simplificado definido para criar rapidamente um VM a partir de uma imagem. O conjunto de parâmetros simplificado utiliza o nome VM para criar automaticamente alguns dos recursos necessários, como o vNet e o endereço IP público, para si. 

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name $vmName `
   -Image $imageDefinition.Id
```



## <a name="full-parameter-set"></a>Conjunto completo de parâmetros

Pode criar um VM utilizando recursos específicos utilizando o conjunto completo de parâmetros.

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

# Network pieces
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
   -DestinationPortRange 3389 `
   -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name myNic `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageDefinition.Id to use the latest image version.
$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```


## <a name="next-steps"></a>Passos seguintes
[O Azure Image Builder (pré-visualização)](./windows/image-builder-overview.md) pode ajudar a automatizar a criação da versão de imagem, pode até usá-la para atualizar e criar uma nova versão de imagem a partir de uma versão de [imagem existente.](./windows/image-builder-gallery-update-image-version.md) 

Também pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [visão geral.](./windows/shared-image-galleries.md) Se tiver problemas, veja as galerias de [imagens partilhadas de Troubleshooting.](./windows/troubleshooting-shared-images.md)