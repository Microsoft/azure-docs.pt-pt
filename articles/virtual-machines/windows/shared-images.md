---
title: Criar imagens de VM partilhadas com o Azure PowerShell | Documentos da Microsoft
description: Saiba como utilizar o Azure PowerShell para criar uma imagem de máquina de virtual partilhado no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 707c642fae4d7698eb3d793b83dc574872925f0f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709135"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Criar uma galeria de imagem partilhada com o Azure PowerShell 

R [Galeria de imagens de partilhado](shared-image-galleries.md) simplifica a imagem personalizada de partilha na sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser utilizadas para iniciar tarefas de implantação, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações de SO. 

A Galeria de imagens partilhado permite que Compartilhe suas imagens VM personalizadas com outras pessoas na sua organização, numa ou em regiões, dentro de um inquilino do AAD. Escolha quais imagens que pretende partilhar, quais são as regiões que pretende tornar disponível no mesmos e a quem pretende partilhá-los com. Pode criar várias galerias, para que pode agrupar logicamente Imagens partilhadas. 

A galeria é um recurso de nível superior que fornece controlo de acesso completa baseada em funções (RBAC). Imagens podem ser atualizadas, e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A Galeria só funciona com imagens gerenciadas pelo.

O recurso da Galeria de imagens de partilhado tem vários tipos de recursos. Podemos será a utilizar ou criar neste artigo:

| Resource | Descrição|
|----------|------------|
| **Imagem gerida** | Esta é uma imagem básica que pode ser usada sozinha ou utilizada para criar uma **versão da imagem** na Galeria de imagens. Imagens geridas são criadas a partir de VMs generalizadas. Uma imagem gerida é um tipo especial de VHD que pode ser utilizado para fazer várias VMs e pode agora ser utilizado para criar versões de imagem partilhada. |
| **Galeria de imagens** | Como o Azure Marketplace, um **Galeria de imagens** é um repositório de gerenciamento e compartilhamento de imagens, mas controlar quem tem acesso. |
| **Definição de imagem** | As imagens são definidas dentro de uma galeria e transportar informações sobre a imagem e os requisitos para utilizá-lo internamente. Isto inclui se a imagem é Windows ou Linux, notas de versão e os requisitos de memória mínimos e máximos. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é o que utiliza para criar uma VM ao utilizar uma galeria. Pode ter várias versões de uma imagem, conforme necessário para o seu ambiente. Como uma imagem gerida, quando utiliza um **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser utilizadas várias vezes. |

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Para concluir o exemplo neste artigo, tem de ter uma imagem gerida existente. Pode seguir [Tutorial: Criar uma imagem personalizada de uma VM do Azure com o Azure PowerShell](tutorial-custom-images.md) para criar um, se necessário. Se a imagem gerida contém um disco de dados, o tamanho do disco de dados não pode ser mais de 1 TB.

Quando trabalhar com este artigo, substitua o grupo de recursos e a VM nomes onde necessário.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Criar VMs a partir de uma imagem

Assim que a versão da imagem for concluída, pode criar uma ou mais VMs novas. Utilizar o [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet. 

Este exemplo cria uma VM com o nome *myVMfromImage*, na *myResourceGroup* no *Centro-Sul* datacenter.


```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Network pieces
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

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Passos Seguintes
[Construtor de imagens do Azure (pré-visualização)](image-builder-overview.md) podem ajudar a automatizar a criação de imagens de versão, ainda pode usá-lo para atualizar e [criar uma nova versão de imagem a partir de uma versão de imagem existente](image-builder-gallery-update-image-version.md). 

Também pode criar recursos de Galeria de imagens de partilhado através de modelos. Vários modelos de início rápido do Azure estão disponíveis: 

- [Criar uma galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir da versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para obter mais informações sobre as galerias de imagem de partilhado, consulte a [descrição geral](shared-image-galleries.md). Caso se depare com problemas, consulte [resolução de problemas partilhado galerias de imagem](troubleshooting-shared-images.md).

