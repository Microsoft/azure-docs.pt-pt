---
title: Implementar aplicação de pilha dupla IPv6 - Balanceador de Carga Básica - PowerShell
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implementar uma aplicação de pilha dupla IPv6 na rede virtual Azure usando Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a8b9e82f71be83254a263ae28598a43dd7c8b9f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95994456"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell"></a>Implementar uma aplicação de pilha dupla IPv6 utilizando o Balancer de Carga Básica - PowerShell

Este artigo mostra-lhe como implementar uma aplicação dual stack (IPv4 + IPv6) com Balanceador de Carga Básica utilizando a Azure PowerShell que inclui uma rede virtual de dupla pilha e sub-rede, um Balanceador de Carga Básica com configurações frontais dual (IPv4 + IPv6), VMs com NICs que têm uma configuração IP dupla, grupo de segurança de rede e IPs públicos.

Para implementar uma aplicação de pilha dupla (IPV4 + IPv6) utilizando o Balanceador de Carga Padrão, consulte [implementar uma aplicação de dupla pilha IPv6 com o Balanceador de Carga Padrão utilizando a Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 6.9.0 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar a sua rede virtual de dupla pilha, tem de criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myRGDualStack* na localização *leste dos EUA:*

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Criar endereços IP públicos IPv4 e IPv6
Para aceder às suas máquinas virtuais a partir da Internet, necessita de endereços IP públicos IPv4 e IPv6 para o equilibrador de carga. Crie endereços IP públicos com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O exemplo a seguir cria endereço IP público IPv4 e IPv6 nomeado *dsPublicIP_v4* e *dsPublicIP_v6* no grupo de recursos *dsRG1:*

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
Para aceder às suas máquinas virtuais utilizando uma ligação RDP, crie um IPV4 endereços IP públicos para as máquinas virtuais com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Criar Balanceador de Carga Básico

Nesta secção, configura o DUPLO FRONTEND IP (IPv4 e IPv6) e o conjunto de endereços de back-end para o balançador de carga e, em seguida, criar um Balanceador de Carga Básico.

### <a name="create-front-end-ip"></a>Criar IP frontal

Crie um IP frontal com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). O exemplo a seguir cria configurações IP de frontend IPv4 e IPv6 denominada *dsLbFrontEnd_v4* e *dsLbFrontEnd_v6:*

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Configure piscina de endereço de fundo

Crie uma piscina de endereços back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Os VMs ligam-se a esta piscina traseira nos degraus restantes. O exemplo a seguir cria piscinas de endereços back-end denominada *dsLbBackEndPool_v4* e *dsLbBackEndPool_v6* para incluir VMs com configurações IPV4 e IPv6 NIC:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento
Utilize [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) para criar uma sonda de saúde para monitorizar a saúde dos VMs.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Para garantir que apenas VMs saudáveis recebem tráfego, você pode opcionalmente definir uma sonda de saúde. O balanceador de carga básico utiliza uma sonda IPv4 para avaliar a saúde tanto para os pontos finais IPv4 como IPv6 nos VMs. O balanceador de carga padrão inclui suporte para sondas de saúde IPv6 explicitamente.

Crie uma regra do balançador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). O exemplo a seguir cria regras de compensação de carga denominada *dsLBrule_v4* e *dsLBrule_v6* e equilibra o tráfego na porta *TCP* *80* para as configurações IP de frontend IPv4 e IPv6:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe
```

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Criar o Balanceador de Carga Básica com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo a seguir cria um Balancer de Carga Básica público chamado *myLoadBalancer* utilizando as configurações IP de frontend IPv4 e IPv6, piscinas de backend e regras de equilíbrio de carga que criou nos passos anteriores:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Criar recursos de rede
Antes de implementar alguns VMs e poder testar o seu balanceador, tem de criar recursos de rede de suporte - conjunto de disponibilidade, grupo de segurança de rede, rede virtual e NICs virtuais. 
### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Para melhorar a elevada disponibilidade da aplicação, coloque as VMs num conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). O exemplo seguinte cria um conjunto de disponibilidade com o nome *myAvailabilitySet*:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Criar grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que regem a comunicação de entrada e saída no seu VNET.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Criar uma regra de grupo de segurança de rede para a porta 3389

Crie uma regra do grupo de segurança de rede para permitir ligações RDP através da porta 3389 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
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

```azurepowershell-interactive
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

Criar um grupo de segurança de rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual designada *myVnet* com *mySubnet*:

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Criar NICs

Crie NICs virtuais com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). O exemplo a seguir cria dois NICs virtuais, ambos com configurações IPv4 e IPv6. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes).

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
      
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Defina um nome de utilizador e palavra-passe para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Agora pode criar os VMs com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria dois VMs e os componentes de rede virtuais necessários se ainda não existirem. 

```azurepowershell-interactive
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

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Determinar endereços IP dos pontos finais IPv4 e IPv6
Obtenha todos os Objetos de Interface de Rede no grupo de recursos para resumir os IP utilizados nesta implementação com `get-AzNetworkInterface` . Além disso, obtenha os endereços frontend do Balancer de Carga dos pontos finais IPv4 e IPv6 com `get-AzpublicIpAddress` .

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
O seguinte número mostra uma saída de amostra que lista os endereços privados IPv4 e IPv6 dos dois VMs, e os endereços IP frontend IPv4 e IPv6 do Balanceador de Carga.

![Resumo IP da implementação de aplicação de pilha dupla (IPv4/IPv6) em Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Ver rede virtual de pilha dupla IPv6 no portal Azure
Pode ver a rede virtual de pilha dupla IPv6 no portal Azure da seguinte forma:
1. Na barra de pesquisa do portal, *insira o DSVnet*.
2. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a. Isto lança a página **geral** da rede virtual dual stack chamada *dsVnet*. A rede virtual dual stack mostra os dois NICs com configurações IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *dsSubnet*.

  ![IPv6 dual stack rede virtual em Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um Balanceador de Carga Básica com uma configuração IP de dois frontend (IPv4 e IPv6). Também criou duas máquinas virtuais que incluíam NICs com configurações IP duplas (IPV4 + IPv6) que foram adicionadas ao pool back-end do equilibrista de carga. Para saber mais sobre o suporte do IPv6 nas redes virtuais Azure, veja [o que é o IPv6 para a Rede Virtual Azure?](ipv6-overview.md)
