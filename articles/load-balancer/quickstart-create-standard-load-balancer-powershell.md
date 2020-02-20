---
title: 'Quickstart: Criar um Balancer de Carga - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um Balancer de Carga usando o Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 0cd2bb54bb436beaa933195b88bc6f13a1b23e6f
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470450"
---
# <a name="quickstart-create-a-load-balancer-using-azure-powershell"></a>Quickstart: Criar um Balancer de carga utilizando o Azure PowerShell

Este início rápido mostra-lhe como criar um Balanceador de Carga Standard com o Azure PowerShell. Para testar o equilíbrio de carga, implementa três máquinas virtuais (VMs) que executam o servidor do Windows e equilibra uma aplicação web entre os VMs. Para saber mais sobre o Balanceador de Carga Standard, veja [O que é o Balanceador de Carga Standard](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Antes de poder criar o seu equilibrador de carga, tem de criar um grupo de recursos com o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroupSLB* na localização *EastUS:*

```azurepowershell
$rgName='MyResourceGroupSLB'
$location='eastus'
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à sua aplicação na Internet, precisa de um endereço IP público para o balanceador de carga. Crie um endereço IP público com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O exemplo seguinte cria um endereço IP público redundante da zona chamado *myPublicIP* no grupo de recursos *myResourceGroupSLB:*

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
```

Para criar um endereço IP público zonal na zona 1, utilize o seguinte:

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
 -zone 1
```

Use ```-SKU Basic``` para criar um IP Público Básico. Os IPs públicos básicos não são compatíveis com o equilíbrio de carga **Standard.** A Microsoft recomenda a utilização **do Standard** para cargas de trabalho de produção.

> [!IMPORTANT]
> O resto deste quickstart assume que o **Standard** SKU é escolhido durante o processo de seleção SKU acima.

## <a name="create-load-balancer"></a>Criar Balanceador de carga

Nesta secção, configura o IP frontal e o conjunto de endereços traseiros para o equilibrador de carga e, em seguida, cria o Equilíbrio de Carga Padrão.

### <a name="create-frontend-ip"></a>Criar o IP de front-end

Crie um IP frontal com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). O exemplo seguinte cria uma configuração IP frontal chamada *myFrontEnd* e anexa o *endereço myPublicIP:*

```azurepowershell
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Configure piscina de endereço sinuoso

Crie um conjunto de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Os VMs anexam-se a esta piscina traseira nos degraus restantes. O exemplo seguinte cria uma piscina de endereços back-end chamada *myBackEndPool:*

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento
Para permitir ao balanceador de carga monitorizar o estado da aplicação, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. Por predefinição, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos. Pode criar uma sonda de estado de funcionamento com base num protocolo ou numa página de verificação de estado de funcionamento específica da aplicação.

O exemplo seguinte cria uma sonda TCP. Também pode criar sondas HTTP personalizadas para obter verificações de estado de funcionamento mais detalhadas. Quando utilizar uma sonda HTTP personalizada, tem de criar a página de verificação de estado de funcionamento, tal como *healthcheck.aspx*. A sonda tem de devolver uma resposta **HTTP 200 OK** para o balanceador de carga manter o anfitrião na rotação.

Para criar uma sonda de saúde TCP, utilize [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). O exemplo seguinte cria uma sonda de estado de funcionamento denominada *myHealthProbe* que monitoriza cada VM na *porta*  HTTP *80*:

```azurepowershell
$probe = New-AzLoadBalancerProbeConfig `
 -Name 'myHealthProbe' `
 -Protocol Http -Port 80 `
 -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga
É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Para garantir que apenas as VMs em bom estado de funcionamento recebem o tráfego, também pode definir a sonda de estado de funcionamento a utilizar.

Crie uma regra de equilíbrio de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). O exemplo seguinte cria uma regra de balanceador de carga designada *myLoadBalancerRule* e faz o balanceamento de carga do tráfego no *TCP* porta *80*:

```azurepowershell
$rule = New-AzLoadBalancerRuleConfig `
  -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bePool
```

### <a name="create-the-nat-rules"></a>Criar as regras NAT

Crie regras NAT com [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). O exemplo seguinte cria regras NAT chamadas *myLoadBalancerRDP1* e *myLoadBalancerRDP2* para permitir ligações RDP aos servidores de back-end com porta 4221 e 4222:

```azurepowershell
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP1' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp -FrontendPort 4221 `
  -BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP2' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4222 `
  -BackendPort 3389

$natrule3 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP3' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4223 `
  -BackendPort 3389
```

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Crie o Balancer de Carga Padrão com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo seguinte cria um Balancer de Carga Padrão público chamado myLoadBalancer utilizando a configuração IP frontal, piscina de back-end, sonda de saúde, regra de equilíbrio de carga e regras NAT que criou nos passos anteriores:

```azurepowershell
$lb = New-AzLoadBalancer `
  -ResourceGroupName $rgName `
  -Name 'MyLoadBalancer' `
  -SKU Standard `
  -Location $location `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bepool `
  -Probe $probe `
  -LoadBalancingRule $rule `
  -InboundNatRule $natrule1,$natrule2,$natrule3
```

Utilize ```-SKU Basic``` para criar um Equilíbrio de Carga Básico. A Microsoft recomenda a utilização do Standard para cargas de trabalho de produção.

> [!IMPORTANT]
> O resto deste quickstart assume que o **Standard** SKU é escolhido durante o processo de seleção SKU acima.

## <a name="create-network-resources"></a>Criar recursos de rede
Antes de implementar algumas VMs e testar o balanceador, tem de criar recursos de rede de apoio: rede virtual e NICs virtuais. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Criar uma rede virtual com [new-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual designada *myVnet* com *mySubnet*:

```azurepowershell
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupSLB" `
  -Location $location `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Criar endereços IP públicos para os VMs

Para aceder aos seus VMs utilizando uma ligação RDP, necessita de um endereço IP público para os VMs. Uma vez que um Balancer de Carga Padrão é utilizado neste cenário, deve criar endereços IP públicos Standard para os VMs com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static
 

$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static


$RdpPublicIP_3 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_3" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static

```

Use ```-SKU Basic``` para criar um IPs público básico. A Microsoft recomenda a utilização do Standard para cargas de trabalho de produção.

### <a name="create-network-security-group"></a>Criar grupo de segurança de rede
Crie um grupo de segurança de rede para definir ligações recebidas para a sua rede virtual.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Criar uma regra de grupo de segurança de rede para a porta 3389
Crie uma regra do grupo de segurança de rede para permitir ligações RDP através da porta 3389 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell

$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra de grupo de segurança de rede para a porta 80
Crie uma regra do grupo de segurança de rede para permitir ligações de entrada através da porta 80 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar um grupo de segurança de rede com o [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location `
-Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Criar NICs
Crie NICs virtuais e associe-se a endereços IP públicos e grupos de segurança de rede criados nos passos anteriores com [new-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). O exemplo seguinte cria três NICs virtuais. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes). Pode criar NICs virtuais e VMs adicionais em qualquer altura e adicioná-las ao balanceador de carga:

```azurepowershell
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic1' -PublicIpAddress $RdpPublicIP_1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule1 -Subnet $vnet.Subnets[0]

$nicVM2 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic2' -PublicIpAddress $RdpPublicIP_2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule2 -Subnet $vnet.Subnets[0]

$nicVM3 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic3' -PublicIpAddress $RdpPublicIP_3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule3 -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Defina um nome de utilizador e palavra-passe para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = Get-Credential
```

Agora pode criar os VMs com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria dois VMs e os componentes de rede virtual necessários se ainda não existirem. Neste exemplo, os NICs (*MyNic1*, *MyNic2*, e *MyNic3*) criados no passo anterior são atribuídos a máquinas virtuais *myVM1*, *myVM2*e *VM3*. Além disso, uma vez que os NICs estão associados à piscina de backend do equilibrante de carga, os VMs são automaticamente adicionados à piscina de backend.

```azurepowershell

# ############## VM1 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id

# Create a virtual machine
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig

# ############## VM2 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM2' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM2' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM2.Id

# Create a virtual machine
$vm2 = New-AzVM -ResourceGroupName $rgName -Zone 2 -Location $location -VM $vmConfig

# ############## VM3 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
| Add-AzVMNetworkInterface -Id $nicVM3.Id

# Create a virtual machine
$vm3 = New-AzVM -ResourceGroupName $rgName -Zone 3 -Location $location -VM $vmConfig
```

Leva alguns minutos para criar e configurar os três VMs.

### <a name="install-iis-with-a-custom-web-page"></a>Instale o IIS com uma página web personalizada

Instale o IIS com uma página web personalizada em ambos os VMs de back-end da seguinte forma:

1. Obtenha os endereços IP públicos dos três VMs usando `Get-AzPublicIPAddress`.

   ```azurepowershell
     $vm1_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_1").IpAddress
     $vm2_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_2").IpAddress
     $vm3_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_3").IpAddress
    ```
2. Crie ligações remotas de ambiente de trabalho com *o myVM1*, *myVM2*e *myVM3* utilizando os endereços IP públicos dos VMs da seguinte forma: 

   ```azurepowershell    
     mstsc /v:$vm1_rdp_ip
     mstsc /v:$vm2_rdp_ip
     mstsc /v:$vm3_rdp_ip
   
    ```

3. Insira as credenciais para cada VM para iniciar a sessão de RDP.
4. Lance o Windows PowerShell em cada VM e utilize os seguintes comandos para instalar o servidor IIS e atualizar o ficheiro htm predefinido.

    ```azurepowershell
    # Install IIS
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
    ```

5. Feche as ligações RDP com *o myVM1,* *myVM2*e *myVM3*.


## <a name="test-load-balancer"></a>Testar o balanceador de carga
Obtenha o endereço IP público do seu balanceor de carga com [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte obtém o endereço IP para *myPublicIP* criado anteriormente:

```azurepowershell
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupSLB" `
  -Name "myPublicIP" | select IpAddress
```

Em seguida, pode introduzir o endereço IP público num browser. O site é apresentado, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu tráfego, como no seguinte exemplo:

![Testar o balanceador de carga](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Para ver o balanceador de carga a distribuir tráfego pelas três VMs que estão a executar a aplicação, pode forçar a atualização do browser. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupSLB
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um Balancer de Carga Padrão, anexou-lhe VMs, configurou a regra de tráfego do Balancer de Carga, a sonda de saúde e, em seguida, testou o Balancer de Carga. Para saber mais sobre o Azure Load Balancer, continue a colocar [os tutoriais do Azure Load Balancer.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

Saiba mais sobre as zonas de [Balancer de Carga e Disponibilidade.](load-balancer-standard-availability-zones.md)