---
title: Configure preferência de encaminhamento para um VM - Azure PowerShell
description: Saiba como criar um VM com um endereço IP público com escolha de preferência de encaminhamento utilizando o Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.openlocfilehash: 65f5b27ecc7b526c4b3e4f5ed62ac90149df1ce1
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510612"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Configure a preferência de encaminhamento para um VM usando a Azure PowerShell

Este artigo mostra-lhe como configurar a preferência de encaminhamento para uma máquina virtual. O tráfego ligado à Internet a partir do VM será encaminhado através da rede ISP quando escolher a **Internet** como a sua opção de preferência de encaminhamento . O encaminhamento padrão é através da rede global da Microsoft.

Este artigo mostra-lhe como criar uma máquina virtual com um IP público que está definido para encaminhar o tráfego através da rede ISP usando a Azure PowerShell.

> [!IMPORTANT]
> A preferência de encaminhamento está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Se utilizar a Cloud Shell, salte para o passo 2. Abra uma sessão de comando e assine em Azure com `Connect-AzAccount` .
2. Criar um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) O exemplo a seguir cria um grupo de recursos na região de Azure oriental dos EUA:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder às suas máquinas virtuais a partir da Internet, precisa de um endereço IP público. Crie endereços IP públicos com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O exemplo a seguir cria um endereço IP público IPv4 chamado *MyPublicIP* do tipo de preferência de encaminhamento *internet* no grupo de recursos *MyResourceGroup* na região *leste dos EUA:*

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>Criar recursos de rede

Antes de implementar um VM, tem de criar recursos de rede de suporte - grupo de segurança de rede, rede virtual e NIC virtual.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar um grupo de segurança de rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). O exemplo a seguir cria um NSG chamado *myNSG*

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVNET* com *mySubNet:*

### <a name="create-a-subnet"></a>Criar uma sub-rede

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>Criar um NIC

Criar NICs virtuais com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface. O exemplo a seguir cria um NIC virtual.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Defina um nome de utilizador e palavra-passe para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

Agora pode criar o VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria dois VMs e os componentes de rede virtuais necessários se ainda não existirem.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>Permitir o tráfego da rede para o VM

Antes de poder ligar ao endereço IP público a partir da internet, certifique-se de que tem as portas necessárias abertas em qualquer grupo de segurança de rede que possa ter associado à interface de rede, a sub-rede em que a interface de rede está, ou ambas. Pode ver as regras de segurança eficazes para uma interface de rede e a sua sub-rede utilizando o [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)ou [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados.

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a preferência de encaminhamento em endereços IP públicos.](routing-preference-overview.md)
* Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure.
* Saiba mais sobre [as definições de endereço IP público .](virtual-network-public-ip-address.md#create-a-public-ip-address)
