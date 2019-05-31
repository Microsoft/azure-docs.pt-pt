---
title: Utilização partilhada imagens VM para criar um conjunto de dimensionamento no Azure | Documentos da Microsoft
description: Saiba como utilizar o Azure PowerShell para criar imagens de VM partilhadas a utilizar para a implementação de conjuntos de dimensionamento de máquinas virtuais no Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 055242c3118ce9d972d55cdc6a21bf623679a0c1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242056"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Criar e utilizar imagens partilhadas para conjuntos de dimensionamento de máquina virtual com o Azure PowerShell

Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. O serviço de Galeria de imagens de partilhado simplifica bastante a imagem personalizada de partilha na sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de imagens partilhado permite que Compartilhe suas imagens VM personalizadas com outras pessoas na sua organização, numa ou em regiões, dentro de um inquilino do AAD. Escolha quais imagens que pretende partilhar, quais são as regiões que pretende tornar disponível no mesmos e a quem pretende partilhá-los com. Pode criar várias galerias, para que pode agrupar logicamente Imagens partilhadas. 

A galeria é um recurso de nível superior que fornece controlo de acesso completa baseada em funções (RBAC). Imagens podem ser atualizadas, e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A Galeria só funciona com imagens gerenciadas pelo. 

O recurso da Galeria de imagens de partilhado tem vários tipos de recursos. Podemos será a utilizar ou criar neste artigo:

| Resource | Descrição|
|----------|------------|
| **Imagem gerida** | Esta é uma imagem básica que pode ser usada sozinha ou utilizada para criar uma **versão da imagem** na Galeria de imagens. Imagens geridas são criadas a partir de VMs generalizadas. Uma imagem gerida é um tipo especial de VHD que pode ser utilizado para fazer várias VMs e pode agora ser utilizado para criar versões de imagem partilhada. |
| **Galeria de imagens** | Como o Azure Marketplace, um **Galeria de imagens** é um repositório de gerenciamento e compartilhamento de imagens, mas controlar quem tem acesso. |
| **Definição de imagem** | As imagens são definidas dentro de uma galeria e transportar informações sobre a imagem e os requisitos para utilizá-lo internamente. Isto inclui se a imagem é Windows ou Linux, notas de versão e os requisitos de memória mínimos e máximos. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é o que utiliza para criar uma VM ao utilizar uma galeria. Pode ter várias versões de uma imagem, conforme necessário para o seu ambiente. Como uma imagem gerida, quando utiliza um **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser utilizadas várias vezes. |

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Os passos abaixo detalham como tornar uma VM existente numa imagem personalizada reutilizável que pode utilizar para criar novas instâncias da VM.

Para concluir o exemplo neste artigo, tem de ter uma imagem gerida existente. Pode seguir [Tutorial: Criar e utilizar uma imagem personalizada para conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell](tutorial-use-custom-image-powershell.md) para criar um, se necessário. Se a imagem gerida contém um disco de dados, o tamanho do disco de dados não pode ser mais de 1 TB.

Quando trabalhar com o artigo, substitua o grupo de recursos e a VM nomes onde necessário.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Criar uma escala definido a partir da versão de imagem partilhada

Criar um conjunto com de dimensionamento de máquina virtual [New-AzVmss](/powershell/module/az.compute/new-azvmss). O exemplo seguinte cria um conjunto da nova versão de imagem no dimensionamento do *Centro-Sul* datacenter. Quando lhe for pedido, defina suas próprias credenciais administrativas para as instâncias VM no conjunto de dimensionamento:


```azurepowershell-interactive
# Define variables
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"
$cred = Get-Credential

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a netowrking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -Id $imageVersion.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]


## <a name="next-steps"></a>Passos Seguintes

Também pode criar recursos de Galeria de imagens de partilhado através de modelos. Vários modelos de início rápido do Azure estão disponíveis: 

- [Criar uma galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir da versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para obter mais informações sobre as galerias de imagem de partilhado, consulte a [descrição geral](shared-image-galleries.md). Caso se depare com problemas, consulte [resolução de problemas partilhado galerias de imagem](troubleshooting-shared-images.md).
