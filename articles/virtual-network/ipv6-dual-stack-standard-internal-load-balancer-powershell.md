---
title: Implemente uma aplicação de dupla pilha IPv6 utilizando o Balancer de Carga Interna Padrão em Azure - PowerShell
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implementar uma aplicação de dupla pilha IPv6 com o Standard Internal Load Balancer na rede virtual Azure utilizando o Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333368"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Implemente uma aplicação de dupla pilha IPv6 utilizando o Balancer de Carga Interna Padrão em Azure - PowerShell (Pré-visualização)

Este artigo mostra-lhe como implementar uma aplicação de dupla pilha (IPv4 + IPv6) no Azure que inclui uma rede virtual de dupla pilha e subnet, um Balancer de Carga Interna Standard com configurações frontais duplas (IPv4 + IPv6), VMs com NICs que têm um IP duplo configuração, grupo de segurança de rede e IPs públicos.

> [!Important]
> O suporte iPv6 para a Rede Virtual Azure está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

O procedimento para criar um Balancer de Carga Interna com capacidade para iPv6 é quase idêntico ao processo de criação de um Equilíbrio de Carga IPv6 virado para a Internet descrito [aqui](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). As únicas diferenças para a criação de um equilibrador de carga interno estão na configuração frontal, como ilustrado no exemplo PowerShell abaixo:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

As alterações que fazem do acima de uma configuração frontal do equilíbrio dor de carga interna:
- O `PrivateIpAddressVersion` é especificado como "IPv6"
- O `-PublicIpAddress` argumento foi omitido `-PrivateIpAddress`ou substituído por . Note que o endereço privado deve estar na gama do espaço IP subnet no qual o equilibrador de carga interno será implantado. Se uma `-PrivateIpAddress` estática for omitida, o próximo endereço IPv6 gratuito será selecionado a partir da subnet na qual o Balancer de carga interna é implantado.
- A sub-rede de dupla pilha em que o equilibrador de `-Subnet` `-SubnetId` carga interna será implantado é especificada com um ou um argumento.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 6.9.0 do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="prerequisites"></a>Pré-requisitos
Antes de implementar uma aplicação de dupla pilha no Azure, tem de configurar a sua subscrição para esta função de pré-visualização utilizando o seguinte Azure PowerShell:

Registe-se da seguinte forma:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Leva até 30 minutos para que a inscrição da funcionalidade seja concluída. Pode verificar o seu estado de registo executando o seguinte comando Azure PowerShell: Verifique o registo da seguinte forma:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Depois de concluída a inscrição, execute o seguinte comando:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar a sua rede virtual de dupla stack, tem de criar um grupo de recursos com o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos chamado *dsStd_ILB_RG* na localização *leste dos EUA:*

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Crie endereços IP públicos IPv4 e IPv6
Para aceder às suas máquinas virtuais a partir da Internet, necessita de endereços IP públicos IPv4 e IPv6 para os VMs. Crie endereços IP públicos com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O exemplo seguinte cria endereço IP público IPv4 e IPv6 chamado *RdpPublicIP_1* e *RdpPublicIP_2* no grupo de recursos *dsStd_ILB_RG:*

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Criar a rede virtual e a subnet

Crie uma rede virtual utilizando [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) com uma configuração de sub-rede utilizando [new-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo seguinte cria uma rede virtual chamada *dsVnet* com *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Criar o Balanceador de Carga Standard

Nesta secção, configura-se o IP frontal duplo (IPv4 e IPv6) e o conjunto de endereços traseiros para o equilibrador de carga e, em seguida, cria um Balancer de Carga Padrão.

### <a name="create-front-end-ip"></a>Criar IP frontal

Crie um IP frontal com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). O exemplo seguinte cria configurações IP frontend IPv4 e IPv6 chamadas *dsLbFrontEnd_v4* e *dsLbFrontEnd_v6:*

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Configure piscina de endereço sinuoso

Crie um conjunto de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Os VMs anexam-se a esta piscina traseira nos degraus restantes. O exemplo seguinte cria piscinas de endereços de back-end chamadas *dsLbBackEndPool_v4* e *dsLbBackEndPool_v6* para incluir VMs com configurações de IPV4 e IPv6 NIC:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Para garantir que apenas VMs saudáveis recebem tráfego, você pode definir opcionalmente uma sonda de saúde. O equilíbrio de carga básico usa uma sonda IPv4 para avaliar a saúde tanto para os pontos finais IPv4 como IPv6 nos VMs. O equilíbrio de carga padrão inclui suporte para sondas de saúde iPv6 explicitamente.

Crie uma regra de equilíbrio de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). O exemplo seguinte cria regras de equilíbrio de carga chamadas *dsLBrule_v4* e *dsLBrule_v6* e equilibra o tráfego na porta *80* *do TCP* para as configurações IP frontend IPv4 e IPv6:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Crie um Balancer de carga padrão com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo seguinte cria um Balancer de Carga Padrão público chamado *myInternalLoadBalancer* utilizando as configurações IP iPv4 e IPv6 frontend, piscinas de backend e regras de equilíbrio de carga que criou nos passos anteriores:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Criar recursos de rede
Antes de implementar alguns VMs e testar o seu balancer, deve criar recursos de rede de suporte - conjunto de disponibilidade, grupo de segurança de rede e NICs virtuais. 

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Para melhorar a elevada disponibilidade da sua aplicação, coloque os seus VMs num conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). O exemplo seguinte cria um conjunto de disponibilidade denominado *dsAVset:*

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Criar grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que regem a comunicação de entrada e saída no seu VNet.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Criar uma regra de grupo de segurança de rede para a porta 3389

Crie uma regra do grupo de segurança de rede para permitir ligações RDP através da porta 3389 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra de grupo de segurança de rede para a porta 80

Crie uma regra do grupo de segurança de rede para permitir ligações à Internet através da porta 80 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar um grupo de segurança de rede com o [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Criar NICs

Crie NICs virtuais com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). O exemplo seguinte cria dois NICs virtuais, tanto com configurações IPv4 e IPv6. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Defina um nome de utilizador e palavra-passe para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Agora pode criar os VMs com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria dois VMs e os componentes de rede virtual necessários se ainda não existirem.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Ver rede virtual de dupla pilha IPv6 no portal Azure
Pode ver a rede virtual iPv6 dual stack no portal Azure da seguinte forma:
1. Na barra de pesquisa do portal, introduza *dsVnet*.
2. Quando a **dsVnet** aparecer nos resultados da pesquisa, selecione-a. Isto lança a página **de visão geral** da rede virtual dual stack chamada *dsVnet*. A rede virtual dual stack mostra os dois NICs com configurações IPv4 e IPv6 localizadas na subnet dual stack chamada *dsSubnet*.

![Rede virtual iPv6 Dual Stack com Balancer de Carga Interna Padrão](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> O IPv6 para a rede virtual Azure está disponível no portal Azure apenas para este lançamento de pré-visualização.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um Balancer de Carga Padrão com uma configuração IP frontal dupla (IPv4 e IPv6). Também criou duas máquinas virtuais que incluíam NICs com configurações IP duplas (IPV4 + IPv6) que foram adicionadas ao pool de back-end do equilibrante de carga. Para saber mais sobre o suporte iPv6 em redes virtuais Azure, veja [o que é o IPv6 para a Rede Virtual Azure?](ipv6-overview.md)