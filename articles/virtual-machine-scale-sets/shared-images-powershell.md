---
title: Usar imagens de VM compartilhadas para criar um conjunto de dimensionamento no Azure
description: Saiba como usar o Azure PowerShell para criar imagens de VM compartilhadas a serem usadas para implantar conjuntos de dimensionamento de máquinas virtuais no Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 5f4eca88614a98f0caf87d04847029328042edd8
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368731"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Criar e usar imagens compartilhadas para conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell

Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. O serviço da Galeria de imagens compartilhadas simplifica muito o compartilhamento de imagens personalizadas em sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de imagens compartilhadas permite que você compartilhe suas imagens de VM personalizadas com outras pessoas em sua organização, dentro ou entre regiões, dentro de um locatário do AAD. Escolha quais imagens você deseja compartilhar, em quais regiões você deseja disponibilizá-las e com quem você deseja compartilhá-las. Você pode criar várias galerias para que seja possível agrupar logicamente imagens compartilhadas. 

A galeria é um recurso de nível superior que fornece RBAC (controle de acesso baseado em função) completo. As imagens podem ter controle de versão e você pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria só funciona com imagens gerenciadas. 

O recurso da Galeria de imagens compartilhadas tem vários tipos de recursos. Usaremos ou compilaremos esses artigos neste artigo:

| Recurso | Descrição|
|----------|------------|
| **Imagem gerida** | Esta é uma imagem básica que pode ser usada sozinha ou usada para criar uma versão de **imagem** numa galeria de imagens. As imagens gerenciadas são criadas a partir de VMs generalizadas. Uma imagem gerida é um tipo especial de VHD que pode ser usado para fazer vários VMs e agora pode ser usado para criar versões de imagem partilhada. |
| **Galeria de imagens** | Tal como o Azure Marketplace, uma galeria de **imagens** é um repositório para gerir e partilhar imagens, mas controla quem tem acesso. |
| **Definição de imagem** | As imagens são definidas em uma galeria e contêm informações sobre a imagem e os requisitos para usá-las internamente. Isso inclui se a imagem é Windows ou Linux, notas de versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. |
| **Versão de imagem** | Uma **versão de imagem** é o que se usa para criar um VM quando se utiliza uma galeria. Pode ter várias versões de uma imagem necessária para o seu ambiente. Como uma imagem gerida, quando se usa uma versão de **imagem** para criar um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes. |

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Os passos abaixo detalham como tornar uma VM existente numa imagem personalizada reutilizável que pode utilizar para criar novas instâncias da VM.

Para concluir o exemplo neste artigo, você deve ter uma imagem gerenciada existente. Pode seguir o Tutorial: Criar e utilizar uma imagem personalizada para conjuntos de escala de [máquinas virtuais com o Azure PowerShell](tutorial-use-custom-image-powershell.md) para criar um, se necessário. Se a imagem gerenciada contiver um disco de dados, o tamanho do disco de dados não poderá ser superior a 1 TB.

Ao trabalhar no artigo, substitua os nomes do grupo de recursos e da VM quando necessário.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Criar um conjunto de dimensionamento da versão da imagem compartilhada

Crie um conjunto de máquinas virtuais com [New-AzVmss](/powershell/module/az.compute/new-azvmss). O exemplo seguinte cria uma escala definida a partir da nova versão de imagem no centro de dados *do Centro Sul dos EUA.* Quando solicitado, defina suas próprias credenciais administrativas para as instâncias de VM no conjunto de dimensionamento:


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
  -ImageReferenceId $imageVersion.Id

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


## <a name="next-steps"></a>Passos seguintes

Você também pode criar recursos da Galeria de imagens compartilhadas usando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar uma Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem numa Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar um VM a partir da versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [visão geral.](shared-image-galleries.md) Se tiver problemas, veja as galerias de [imagens partilhadas de Troubleshooting.](troubleshooting-shared-images.md)
