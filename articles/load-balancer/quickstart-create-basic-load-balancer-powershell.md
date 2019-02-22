---
title: 'Início rápido: Criar um balanceador de carga básico - Azure PowerShell'
titlesuffix: Azure Load Balancer
description: Este início rápido mostra como criar um Balanceador de Carga Básico com o PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms:custom: seodec18
ms.openlocfilehash: e03d7686b89cd72446a7642921f162bc14a684d3
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591521"
---
# <a name="get-started"></a>Início rápido: Criar um balanceador de carga público com o Azure PowerShell

Este início rápido mostra como criar um Balanceador de Carga Básico com o Azure PowerShell. Para testar o balanceador de carga, implemente duas máquinas virtuais (VMs) com o Windows Server e faça o balanceamento de carga de uma aplicação Web entre as VMs.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar o Balanceador de carga, tem de criar um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupLB* na localização *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público
Para aceder à sua aplicação na Internet, precisa de um endereço IP público para o balanceador de carga. Criar um endereço IP público [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O exemplo seguinte cria um endereço IP público denominado *myPublicIP* no grupo de recursos *myResourceGroupLB*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

## <a name="create-basic-load-balancer"></a>Criar Balanceador de Carga Básico

Nesta secção, configure o IP de front-end e o conjunto de endereços de back-end do Balanceador de carga e, em seguida, criar o Balanceador de carga básico.

### <a name="create-front-end-ip"></a>Criar endereço IP de front-end

Criar um IP de front-end com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). O exemplo seguinte cria uma configuração de IP de front-end denominada *myFrontEnd* e anexa o endereço *myPublicIP*:

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-back-end-address-pool"></a>Configurar o conjunto de endereços de back-end

Criar um conjunto de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). As VMs são anexadas a este conjunto de back-end nos restantes passos. O exemplo seguinte cria um conjunto de endereços de back-end designado *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento
Para permitir ao balanceador de carga monitorizar o estado da aplicação, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. Por predefinição, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos. Pode criar uma sonda de estado de funcionamento com base num protocolo ou numa página de verificação de estado de funcionamento específica da aplicação. 

O exemplo seguinte cria uma sonda TCP. Também pode criar sondas HTTP personalizadas para obter verificações de estado de funcionamento mais detalhadas. Quando utilizar uma sonda HTTP personalizada, tem de criar a página de verificação de estado de funcionamento, tal como *healthcheck.aspx*. A sonda tem de devolver uma resposta **HTTP 200 OK** para o balanceador de carga manter o anfitrião na rotação.

Para criar uma sonda de estado de funcionamento TCP, utilize [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). O exemplo seguinte cria uma sonda de estado de funcionamento denominada *myHealthProbe* que monitoriza cada VM na *porta*  HTTP *80*:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Para garantir que apenas as VMs em bom estado de funcionamento recebem o tráfego, também pode definir a sonda de estado de funcionamento a utilizar.

Crie uma regra de Balanceador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). O exemplo seguinte cria uma regra de balanceador de carga designada *myLoadBalancerRule* e faz o balanceamento de carga do tráfego no *TCP* porta *80*:

```azurepowershell-interactive
$lbrule = New-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Criar as regras NAT

Criar com as regras NAT [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). O exemplo seguinte cria regras NAT denominadas *myLoadBalancerRDP1* e *myLoadBalancerRDP2* para permitir ligações RDP aos servidores de back-end através das portas 4221 e 4222:

```azurepowershell-interactive
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Criar o Balanceador de carga básico com [novo AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo seguinte cria um Balanceador de Carga Básico público denominado myLoadBalancer através da configuração de IP de front-end, conjunto de back-end, sonda de estado de funcionamento, regra de balanceamento de carga e regras NAT que criou nos passos anteriores:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Criar recursos de rede

Antes de implementar algumas VMs e testar o balanceador, tem de criar recursos de rede de apoio: rede virtual e NICs virtuais. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual designada *myVnet* com *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Criar grupo de segurança de rede

Crie um grupo de segurança de rede para definir ligações recebidas para a sua rede virtual.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Criar uma regra de grupo de segurança de rede para a porta 3389

Criar uma regra de grupo de segurança de rede para permitir ligações de RDP pela porta 3389 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra de grupo de segurança de rede para a porta 80

Criar uma regra de grupo de segurança de rede para permitir ligações de entrada através da porta 80 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Criar NICs

Criar NICs virtuais criados com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). O exemplo seguinte cria duas NICs virtuais. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes). Pode criar NICs virtuais e VMs adicionais em qualquer altura e adicioná-las ao balanceador de carga:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para melhorar a elevada disponibilidade da aplicação, coloque as VMs num conjunto de disponibilidade.

Criar um conjunto de disponibilidade com [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). O exemplo seguinte cria um conjunto de disponibilidade designado *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Defina um nome de utilizador e palavra-passe para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, pode criar as VMs com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria duas VMs e os componentes de rede virtual necessários, se eles ainda não existir. Durante a criação de VM exemplo abaixo, os NICs criados anteriormente estão associados com as VMs, uma vez que são atribuídas da mesma rede virtual (*myVnet*) e a sub-rede (*mySubnet*):

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

O parâmetro `-AsJob` cria a VM como uma tarefa em segundo plano, para que os pedidos do PowerShell voltem para si. Pode ver os detalhes das tarefas em segundo plano com o cmdlet `Job`. Demora alguns minutos para criar e configurar as duas VMs.

### <a name="install-iis-with-custom-web-page"></a>Instalar o IIS com uma página Web personalizada
 
Instale o IIS com uma página Web personalizada em ambas as VMs de back-end da seguinte forma:

1. Obtenha o endereço IP público do Balanceador de Carga. Obtenha o endereço IP público do Balanceador de Carga com `Get-AzPublicIPAddress`.

  ```azurepowershell-interactive
    Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
  ```
2. Crie uma ligação de ambiente de trabalho remoto a VM1 com o endereço IP público que obteve no passo anterior. 

  ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
  ```
3. Introduza as credenciais para *VM1* para iniciar a sessão de RDP.
4. Inicie o Windows PowerShell na VM1 e utilize os seguintes comandos para instalar o servidor de ISS e atualizar o ficheiro html predefinido.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Feche a ligação RDP a *myVM1*.
6. Crie uma ligação de RDP a *myVM2* ao executar o comando `mstsc /v:PublicIpAddress:4222` e repita o passo 4 para *VM2*.

## <a name="test-load-balancer"></a>Testar o balanceador de carga
Obtenha o endereço IP público do seu Balanceador de carga com [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte obtém o endereço IP para *myPublicIP* criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

Em seguida, pode introduzir o endereço IP público num browser. O site é apresentado, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu tráfego, como no seguinte exemplo:

![Testar o balanceador de carga](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Para ver o balanceador de carga a distribuir tráfego pelas duas VMs que estão a executar a aplicação, pode forçar a atualização do browser.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) de comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de introdução, criou o Balanceador de Carga Básico, anexou VMs ao mesmo, configurou a regra de tráfego do balanceador de carga, a sonda de estado de funcionamento e, em seguida, testou o balanceador de carga. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-basic-internal-portal.md)