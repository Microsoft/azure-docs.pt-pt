---
title: Criar imagens VM partilhadas com a Azure PowerShell
description: Aprenda a usar o Azure PowerShell para criar uma imagem de máquina virtual partilhada no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: db877c96167fc011c1a8bd52cc1d0b63260007c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74066246"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Crie uma galeria de imagens partilhada com a Azure PowerShell 

Uma [Galeria de Imagem Partilhada](shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser usadas para tarefas de implementação de botas como aplicações de pré-carregamento, configurações de aplicações e outras configurações de SO. 

A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras da sua organização, dentro ou em todas as regiões, dentro de um inquilino da AAD. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. Você pode criar várias galerias para que você possa logicamente agrupar imagens partilhadas. 

A galeria é um recurso de alto nível que fornece um controlo completo de acesso baseado em papéis (RBAC). As imagens podem ser versonizadas e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria só funciona com Imagens Geridas.

A funcionalidade Da Galeria de Imagem Partilhada tem vários tipos de recursos. Vamos usar ou construir estes neste artigo:

| Recurso | Descrição|
|----------|------------|
| **Imagem gerida** | Esta é uma imagem básica que pode ser usada sozinha ou usada para criar uma versão de **imagem** numa galeria de imagens. As imagens geridas são criadas a partir de VMs generalizados. Uma imagem gerida é um tipo especial de VHD que pode ser usado para fazer vários VMs e agora pode ser usado para criar versões de imagem partilhada. |
| **Galeria de imagens** | Tal como o Azure Marketplace, uma galeria de **imagens** é um repositório para gerir e partilhar imagens, mas controla quem tem acesso. |
| **Definição de imagem** | As imagens são definidas dentro de uma galeria e transportam informações sobre a imagem e requisitos para a sua utilização interna. Isto inclui se a imagem é Windows ou Linux, notas de lançamento e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. |
| **Versão de imagem** | Uma **versão de imagem** é o que se usa para criar um VM quando se utiliza uma galeria. Pode ter várias versões de uma imagem necessária para o seu ambiente. Como uma imagem gerida, quando se usa uma versão de **imagem** para criar um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes. |

Por cada 20 VMs que cria si multânios, recomendamos que mantenha uma réplica. Por exemplo, se estiver a criar 120 VMs simultaneamente usando a mesma imagem numa região, sugerimos que mantenha pelo menos 6 réplicas da sua imagem. Para mais informações, consulte [Scaling](/azure/virtual-machines/windows/shared-image-galleries#scaling).

## <a name="before-you-begin"></a>Antes de começar

Para completar o exemplo neste artigo, deve ter uma imagem gerida existente. Pode seguir o [Tutorial: Criar uma imagem personalizada de um Azure VM com A PowerShell Azure](tutorial-custom-images.md) para criar um, se necessário. Se a imagem gerida contiver um disco de dados, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar através deste artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Criar VMs a partir de uma imagem

Uma vez que a versão de imagem esteja completa, pode criar um ou mais Novos VMs. Utilizando o cmdlet [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 

Este exemplo cria um VM chamado *myVMfromImage,* no *myResourceGroup* no centro de dados *sul-americano.*


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

## <a name="next-steps"></a>Passos seguintes
[O Azure Image Builder (pré-visualização)](image-builder-overview.md) pode ajudar a automatizar a criação da versão de imagem, pode até usá-la para atualizar e criar uma nova versão de imagem a partir de uma versão de [imagem existente.](image-builder-gallery-update-image-version.md) 

Também pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar uma Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem numa Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar um VM a partir da versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [visão geral.](shared-image-galleries.md) Se tiver problemas, veja as galerias de [imagens partilhadas de Troubleshooting.](troubleshooting-shared-images.md)

