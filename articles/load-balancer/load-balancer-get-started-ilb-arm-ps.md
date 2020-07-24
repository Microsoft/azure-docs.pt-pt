---
title: Criar um balançador de carga interno Azure utilizando o PowerShell
titleSuffix: Azure Load Balancer
description: Saiba como utilizar o módulo do Azure PowerShell com o Azure Resource Manager para criar um balanceador de carga interno
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/02/2020
ms.author: allensu
ms.openlocfilehash: 7e962b87b1bd7ef200aa276d8dbecffd5d3fed04
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090438"
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Criar um balanceador de carga interno com o módulo do Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Introdução à configuração

Este artigo explica como utilizar o Azure Resource Manager com o módulo do Azure PowerShell para criar um balanceador de carga interno. No modelo de implementação Resource Manager, os objetos necessários à criação do balanceador de carga interno são configurados individualmente. Depois de serem criados e configurados, são combinados, de modo a criar o balanceador de carga.

Para implementar um balanceador de carga, têm de ser criados os objetos seguintes:

* Conjunto de IPs de front-end: o endereço IP privado para todo o tráfego de rede de entrada.
* Conjunto de endereços de back-end: as interfaces de rede que vão receber o tráfego com balanceamento de carga a partir do endereço IP do front-end.
* Regras de balanceamento de carga: a configuração da porta (origem e local) para o balanceador de carga.
* Configuração de sonda: as sondas de estado de funcionamentos para máquinas virtuais.
* Regras NAT de entrada: as regras de porta para acesso direto às máquinas virtuais.

Para obter mais informações sobre os componentes do balançador de carga, consulte [os componentes do Balançador de Carga Azure](components.md).

Os seguintes passos explicam como configurar um balanceador de carga entre duas máquinas virtuais.

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar o PowerShell para utilizar o Resource Manager

Certifique-se de que tem a versão mais recente de produção do módulo do Azure PowerShell. O PowerShell tem de estar configurado corretamente para aceder à sua subscrição do Azure.

### <a name="step-1-start-powershell"></a>Passo 1: Iniciar o PowerShell

Inicie o módulo do PowerShell para o Azure Resource Manager.

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="step-2-view-your-subscriptions"></a>Passo 2: Ver as suas subscrições

Verifique as suas subscrições do Azure disponíveis.

```azurepowershell-interactive
Get-AzSubscription
```

Introduza as credenciais quando lhe for pedida a autenticação.

### <a name="step-3-select-the-subscription-to-use"></a>Passo 3: Selecionar a subscrição que pretende utilizar

Escolha qual das suas subscrições do Azure vai utilizar para implementar o balanceador de carga.

```azurepowershell-interactive
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Passo 4: Escolher o grupo de recursos para o balanceador de carga

Crie um grupo de recursos novo para o balanceador de carga. Ignore este passo se estiver a utilizar um grupo de recursos já existente.

```azurepowershell-interactive
New-AzResourceGroup -Name NRP-RG -location "West US"
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. que é utilizada como a predefinição para todos os recursos no grupo de recursos. Utilize sempre o mesmo grupo de recursos para todos os comandos relacionados com a criação do balanceador de carga.

Neste exemplo, criámos um grupo de recursos denominado **NRP-RG**, com a localização E.U.A. Oeste.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Criar a rede virtual e o endereço IP para o conjunto de IPs de front-end

Crie uma sub-rede para a rede virtual e atribua-a à variável **$backendSubnet**.

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Crie uma rede virtual

```azurepowershell-interactive
$vnet= New-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

A rede virtual é criada. A sub-rede **LB-Subnet-BE** é adicionada à rede virtual **NRPVNet**. Estes valores são atribuídos à variável **$vnet**.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Criar o conjunto de IPs de front-end e o conjunto de endereços de back-end

Crie um conjunto de IPs de front-end para o tráfego de entrada e um conjunto de endereços de back-end para receber o tráfego com balanceamento de carga.

### <a name="step-1-create-a-front-end-ip-pool"></a>Passo 1: Criar um conjunto de IPs de front-end

Crie um conjunto de IPs de front-end com o endereço IP privado 10.0.2.5 para a sub-rede 10.0.2.0/24. Este endereço é o ponto final do tráfego de rede de entrada.

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Passo 2: Criar um conjunto de endereços de back-end

Crie um conjunto de endereços de back-end para receber o tráfego de entrada do conjunto de IPs de front-end:

```powershell
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Criar as regras de configuração, a sonda e o balanceador de carga

Quando o conjunto de IPs de front-end e o conjunto de endereços de back-end estiverem criados, especifique as regras para o recurso do balanceador de carga.

### <a name="step-1-create-the-configuration-rules"></a>Passo 1: Criar as regras de configuração

O exemplo cria os quatro objetos de regras seguintes:

* Uma regra NAT de entrada para o Protocolo RDP (Remote Desktop Protocol): redireciona todo o tráfego de entrada na porta 3441 para a porta 3389.
* Uma segunda regra NAT de entrada para RDP: redireciona todo o tráfego de entrada na porta 3442 para a porta 3389.
* Uma regra de sonda de estado de funcionamento: verifica o estado de funcionamento do caminho HealthProbe.aspx.
* Regra do balançador de carga: A carga equilibra todo o tráfego de entrada no porto público 80 para o porto local 80 na piscina de endereços de backend.
* Uma [regra do balançador de carga de portas HA](load-balancer-ha-ports-overview.md) para carregar o equilíbrio de todo o tráfego de entrada em todas as portas para simplificar os cenários de HA para o seu ILB padrão.

```azurepowershell-interactive
$inboundNATRule1= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

$haportslbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="step-2-create-the-load-balancer"></a>Passo 2: Criar o balanceador de carga

Crie o balanceador de carga e combine os objetos de regra (NAT de entrada para RDP, balanceador de carga e sonda de estado de funcionamento):

```azurepowershell-interactive
$NRPLB = New-AzLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -SKU Standard -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

Utilize `-SKU Basic` para criar um Balanceador de Carga Básico. A Microsoft recomenda a utilização do Standard para cargas de trabalho de produção.

## <a name="create-the-network-interfaces"></a>Criar as interfaces de rede

Depois de criar o balanceador de carga interno, defina as interfaces de rede (NICs) que vão receber o tráfego de rede de entrada com balanceamento de carga, as regras NAT e a sonda. Cada interface de rede é configurada individualmente e pode ser atribuída a uma máquina virtual mais tarde. 

### <a name="step-1-create-the-first-network-interface"></a>Passo 1: Criar a primeira interface de rede

Obtenha a rede virtual e a sub-rede do recurso. Estes valores são utilizados para criar as interfaces de rede:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Crie a primeira interface de rede com o nome **lb-nic1-be**. Atribua a interface ao conjunto de back-ends do balanceador de carga. Associe a primeira regra NAT para RDP a esta NIC:

```azurepowershell-interactive
$backendnic1= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Passo 2: Criar a segunda interface de rede

Crie a segunda interface de rede com o nome **lb-nic2-be**. Atribua a segunda interface ao mesmo conjunto de back-ends do balanceador de carga da primeira interface. Associe a segunda NIC à segunda regra NAT para RDP:

```azurepowershell-interactive
$backendnic2= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Reveja a configuração:

```azurepowershell-interactive
$backendnic1
```

As definições devem ser as seguintes:

```output
Name                 : lb-nic1-be
ResourceGroupName    : NRP-RG
Location             : westus
Id                   : /subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
ProvisioningState    : Succeeded
Tags                 :
VirtualMachine       : null
IpConfigurations     : [
                     {
                       "PrivateIpAddress": "10.0.2.6",
                       "PrivateIpAllocationMethod": "Static",
                       "Subnet": {
                         "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                       },
                       "PublicIpAddress": {
                         "Id": null
                       },
                       "LoadBalancerBackendAddressPools": [
                         {
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                         }
                       ],
                       "LoadBalancerInboundNatRules": [
                         {
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                         }
                       ],
                       "ProvisioningState": "Succeeded",
                       "Name": "ipconfig1",
                       "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                       "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                     }
                   ]
DnsSettings          : {
                     "DnsServers": [],
                     "AppliedDnsServers": []
                   }
AppliedDnsSettings   :
NetworkSecurityGroup : null
Primary              : False
```

### <a name="step-3-assign-the-nic-to-a-vm"></a>Passo 3: Atribuir a NIC a uma VM

Utilize o comando `Add-AzVMNetworkInterface` para atribuir a NIC a uma máquina virtual.

Para obter as instruções passo a passo para criar uma máquina virtual e atribuir a NIC, veja [Criar uma VM do Azure com o PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Adicionar a interface de rede

Depois de a máquina virtual ter sido criada, adicione a interface de rede.

### <a name="step-1-store-the-load-balancer-resource"></a>Passo 1: Armazenar o recurso de balanceador de carga

Armazene o recurso do balanceador de carga numa variável (se ainda não o tiver feito). Estamos a usar o nome variável **$lb.** Para os valores de atributos no script, utilize os nomes para os recursos do balanceador de carga que foram criados nos passos anteriores.

```azurepowershell-interactive
$lb = Get-AzLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Passo 2: Armazenar a configuração do back-end

Armazene a configuração do back-end na variável **$backend**.

```azurepowershell-interactive
$backend = Get-AzLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Passo 3: Armazenar a interface de rede

Armazene a interface de rede noutra variável. Este interface foi criado em “Criar interfaces de rede, Passo 1”. Vamos utilizar o nome de variável **$nic1**. Utilize o mesmo nome da interface de rede que no exemplo anterior.

```azurepowershell-interactive
$nic = Get-AzNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Passo 4: Alterar a configuração do back-end

Altere a configuração de back-end na interface de rede.

```azurepowershell-interactive
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Passo 5: Guarde o objeto da interface de rede.

Guarde o objeto da interface de rede.

```azurepowershell-interactive
Set-AzNetworkInterface -NetworkInterface $nic
```

Após a interface ser adicionada ao conjunto de back-ends, é feito o balanceamento de carga ao tráfego de entrada, de acordo com as regras. Estas regras foram configuradas em “Criar as regras de configuração, a sonda e o balanceador de carga”.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Passo 1: Atribuir o objeto de balanceador de carga a uma variável

Atribua o objeto de balanceador de carga (do exemplo anterior) para a variável **$slb** com o comando `Get-AzLoadBalancer`.

```azurepowershell-interactive
$slb = Get-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Passo 2: Adicionar uma regra NAT

Adicione uma regra NAT de entrada nova a um balanceador de carga existente. Utilize a porta 81 para o conjunto de front-ends e a porta 8181 para o conjunto de back-ends:

```azurepowershell-interactive
$slb | Add-AzLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Passo 3: Guardar a configuração

Utilize o comando `Set-AzureLoadBalancer` para guardar a configuração nova.

```azurepowershell-interactive
$slb | Set-AzLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Remover um balanceador de carga existente

Utilize o comando `Remove-AzLoadBalancer` para eliminar o balanceador de carga **NRP-LB** no grupo de recursos **NRP-RG**:

```azurepowershell-interactive
Remove-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Utilize o comutador **-Force** opcional para impedir o pedido de confirmação da eliminação.

## <a name="next-steps"></a>Passos seguintes

* [Configurar o modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)
* [Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
