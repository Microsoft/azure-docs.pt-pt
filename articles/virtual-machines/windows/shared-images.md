---
title: Criar imagens de VM compartilhadas com Azure PowerShell | Microsoft Docs
description: Saiba como usar Azure PowerShell para criar uma imagem de máquina virtual compartilhada no Azure
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
ms.openlocfilehash: 01e6d9dff0ea2c2b60d8e2ab42e39e36d998be83
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390587"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Criar uma galeria de imagens compartilhada com Azure PowerShell 

Uma [Galeria de imagens compartilhadas](shared-image-galleries.md) simplifica o compartilhamento de imagens personalizadas em sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser usadas para inicializar tarefas de implantação, como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações de so. 

A Galeria de imagens compartilhadas permite que você compartilhe suas imagens de VM personalizadas com outras pessoas em sua organização, dentro ou entre regiões, dentro de um locatário do AAD. Escolha quais imagens você deseja compartilhar, em quais regiões você deseja disponibilizá-las e com quem você deseja compartilhá-las. Você pode criar várias galerias para que seja possível agrupar logicamente imagens compartilhadas. 

A galeria é um recurso de nível superior que fornece RBAC (controle de acesso baseado em função) completo. As imagens podem ter controle de versão e você pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria só funciona com imagens gerenciadas.

O recurso da Galeria de imagens compartilhadas tem vários tipos de recursos. Usaremos ou compilaremos esses artigos neste artigo:

| Recurso | Descrição|
|----------|------------|
| **Imagem gerenciada** | Essa é uma imagem básica que pode ser usada sozinha ou usada para criar uma **versão de imagem** em uma galeria de imagens. As imagens gerenciadas são criadas a partir de VMs generalizadas. Uma imagem gerenciada é um tipo especial de VHD que pode ser usado para fazer várias VMs e agora pode ser usado para criar versões de imagens compartilhadas. |
| **Galeria de imagens** | Como o Azure Marketplace, uma **Galeria de imagens** é um repositório para gerenciar e compartilhar imagens, mas você controla quem tem acesso. |
| **Definição de imagem** | As imagens são definidas em uma galeria e contêm informações sobre a imagem e os requisitos para usá-las internamente. Isso inclui se a imagem é Windows ou Linux, notas de versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão de imagem** é o que você usa para criar uma VM ao usar uma galeria. Você pode ter várias versões de uma imagem conforme necessário para seu ambiente. Como uma imagem gerenciada, quando você usa uma **versão de imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. As versões de imagem podem ser usadas várias vezes. |

Para cada 20 VMs que você cria simultaneamente, é recomendável manter uma réplica. Por exemplo, se você estiver criando VMs 120 simultaneamente usando a mesma imagem em uma região, sugerimos que você mantenha pelo menos 6 réplicas da sua imagem. Para obter mais informações, consulte [dimensionamento](/azure/virtual-machines/windows/shared-image-galleries#scaling).

## <a name="before-you-begin"></a>Antes de começar

Para concluir o exemplo neste artigo, você deve ter uma imagem gerenciada existente. Você pode seguir [o tutorial: criar uma imagem personalizada de uma VM do Azure com Azure PowerShell](tutorial-custom-images.md) para criar uma, se necessário. Se a imagem gerenciada contiver um disco de dados, o tamanho do disco de dados não poderá ser superior a 1 TB.

Ao trabalhar com este artigo, substitua os nomes do grupo de recursos e da VM quando necessário.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Criar VMs a partir de uma imagem

Depois que a versão da imagem for concluída, você poderá criar uma ou mais novas VMs. Usando o cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) . 

Este exemplo cria uma VM chamada *myVMfromImage*, no *MyResource* do *sul do EUA Central* datacenter.


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
O [Construtor de imagens do Azure (visualização)](image-builder-overview.md) pode ajudar a automatizar a criação da versão da imagem, até mesmo usá-la para atualizar e [criar uma nova versão da imagem a partir de uma versão de imagem existente](image-builder-gallery-update-image-version.md). 

Você também pode criar recursos da Galeria de imagens compartilhadas usando modelos. Há vários modelos de início rápido do Azure disponíveis: 

- [Criar uma galeria de imagens compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem em uma galeria de imagens compartilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem em uma galeria de imagens compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM com base na versão da imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para obter mais informações sobre galerias de imagens compartilhadas, consulte a [visão geral](shared-image-galleries.md). Se você tiver problemas, consulte a [solução de problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).

