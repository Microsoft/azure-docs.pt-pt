---
title: Crie um conjunto de escala a partir de uma imagem generalizada com Azure PowerShell
description: Crie um conjunto de escala usando uma imagem generalizada numa Galeria de Imagens Partilhadas utilizando o PowerShell.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9edade1aa54d6f4f8160a107f84e6da2e6cf316e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878024"
---
# <a name="create-a-scale-set-from-a-generalized-image-using-powershell"></a>Crie um conjunto de escala a partir de uma imagem generalizada usando PowerShell 

Crie um VM a partir de uma versão de imagem generalizada armazenada numa [Galeria de Imagens Partilhadas.](../virtual-machines/shared-image-galleries.md) Se pretender criar um conjunto de escala utilizando uma imagem especializada, consulte [Configurar instâncias de escala a partir de uma imagem especializada](instance-specialized-image-version-powershell.md).

Uma vez que tenha uma imagem generalizada, pode criar um conjunto de escala de máquina virtual utilizando o cmdlet [New-AzVmss.](/powershell/module/az.compute/new-azvmss) 

Neste exemplo, estamos a usar o ID de definição de imagem para garantir que o seu novo VM utilizará a versão mais recente de uma imagem. Também pode utilizar uma versão específica utilizando o ID da versão de imagem para `-ImageReferenceId` . Por exemplo, para utilizar a versão de imagem *1.0.0* tipo: `-ImageReferenceId "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` . 

Esteja ciente de que usar uma versão de imagem específica significa que a automatização pode falhar se essa versão de imagem específica não estiver disponível porque foi eliminada ou removida da região. Recomendamos a utilização do ID de definição de imagem para criar o seu novo VM, a menos que seja necessária uma versão de imagem específica.


Os exemplos a seguir criam um conjunto de escala chamado *myScaleSet,* no grupo de recursos *myVMSSRG,* na localização *SouthCentralUS.* O conjunto de escalas será criado a partir da imagem *myImageDefinition,* na galeria de imagens *myGallery* no grupo de recursos *myGalleryRG.* Quando solicitado, desagreda as suas credenciais administrativas para as instâncias VM no conjunto de escala.


## <a name="simplified-parameter-set"></a>Conjunto de parâmetros simplificados

Para criar rapidamente um conjunto de escala, ao mesmo tempo que fornece informações mínimas, utilize o parâmetro simplificado definido para criar um conjunto de escala a partir de uma imagem da Galeria de Imagem partilhada.

```azurepowershell-interactive
$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition

# Create user object

$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create the resource group and scale set
New-AzResourceGroup -ResourceGroupName myVMSSRG -Location SouthCentralUS
New-AzVmss `
   -Credential $cred `
   -VMScaleSetName myScaleSet `
   -ImageName $imageDefinition.Id `
   -UpgradePolicyMode Automatic `
   -ResourceGroupName myVMSSRG
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.

## <a name="extended-parameter-set"></a>Conjunto de parâmetros estendidos

Para controlo total de todos os recursos, incluindo o nome, utilize o conjunto de parâmetros completos para criar um conjunto de escala usando uma imagem da Galeria de Imagens Partilhada. 

```azurepowershell-interactive
# Get the image definition

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition

# Create user object

$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."
   
# Define variables for the scale set
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
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
  -ImageReferenceId $imageDefinition.Id

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

## <a name="next-steps"></a>Passos seguintes
[O Azure Image Builder (pré-visualização)](../virtual-machines/image-builder-overview.md) pode ajudar a automatizar a criação de versão de imagem, podendo até usá-la para atualizar e [criar uma nova versão de imagem a partir de uma versão de imagem existente.](../virtual-machines/linux/image-builder-gallery-update-image-version.md) 

Também pode criar recursos da Galeria de Imagens Partilhadas utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [Visão Geral.](../virtual-machines/shared-image-galleries.md) Se tiver problemas, consulte [as galerias de imagem partilhadas .](../virtual-machines/troubleshooting-shared-images.md)