---
title: 'Quickstart: Criar um serviço de ligação privada Azure utilizando a Azure PowerShell'
description: Saiba como criar um serviço de ligação privada Azure utilizando a Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 01/24/2021
ms.author: allensu
ms.openlocfilehash: e8d76e12dea27338e965d8e77871427e9dfabf23
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746684"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Criar um serviço de ligação privada utilizando a Azure PowerShell

Começa a criar um serviço de Ligação Privada que se refere ao teu serviço.  Dê acesso ao Link Privado ao seu serviço ou recurso implantado atrás de um Equilibr de Carga Padrão Azure.  Os utilizadores do seu serviço têm acesso privado a partir da sua rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell instalado localmente ou Azure Cloud Shell

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [o New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```
---
## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Nesta secção, irá criar uma rede virtual e um Equilibrador de Carga Azure interno.

### <a name="virtual-network"></a>Rede virtual

Nesta secção, cria uma rede virtual e uma sub-rede para hospedar o equilibrador de carga que acede ao seu serviço Private Link.

* Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    PrivateLinkServiceNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig
}
$vnet = New-AzVirtualNetwork @net

```

### <a name="create-standard-load-balancer"></a>Criar balanceador de carga padrão

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:

* Crie um IP frontal com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) para o pool IP frontend. Este IP recebe o tráfego de entrada no equilibrador de carga

* Crie um pool de endereços back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para o tráfego enviado a partir do frontend do equilibrador de carga. Esta piscina é onde as máquinas virtuais de backend são implantadas.

* Crie uma sonda de saúde com [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) que determine a saúde dos casos de VM backend.

* Crie uma regra do balançador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) que define como o tráfego é distribuído para os VMs.

* Criar um equilibrador de carga pública com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus2'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-a-private-link-service"></a>Criar um serviço de ligação privada

Nesta secção, crie um serviço de ligação privada que utilize o Balancer de Carga Standard Azure criado no passo anterior.

* Crie a configuração IP do serviço de ligação privada com [New-AzPrivateLinkServiceIpConfig](/powershell/module/az.network/new-azprivatelinkserviceipconfig).

* Crie o serviço de ligação privada com [o New-AzPrivateLinkService](/powershell/module/az.network/new-azprivatelinkservice).

```azurepowershell
## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create the IP configuration for the private link service. ##
$ipsettings = @{
    Name = 'myIPconfig'
    PrivateIpAddress = '10.1.0.5'
    Subnet = $vnet.subnets[0]
}
$ipconfig = New-AzPrivateLinkServiceIpConfig @ipsettings

## Place the load balancer frontend configuration into a variable. ##
$par = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$fe = Get-AzLoadBalancer @par | Get-AzLoadBalancerFrontendIpConfig

## Create the private link service for the load balancer. ##
$privlinksettings = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    LoadBalancerFrontendIpConfiguration = $fe
    IpConfiguration = $ipconfig
}
New-AzPrivateLinkService @privlinksettings

```

O seu serviço de ligação privada foi criado e pode receber tráfego. Se quiser ver fluxos de tráfego, configuure a sua aplicação por trás do seu balanceador de carga padrão.

## <a name="create-private-endpoint"></a>Criar ponto final privado

Nesta secção, você irá mapear o serviço de ligação privada para um ponto final privado. Uma rede virtual contém o ponto final privado para o serviço de ligação privada. Esta rede virtual contém os recursos que irão aceder ao seu serviço de ligação privada.

### <a name="create-private-endpoint-virtual-network"></a>Criar rede virtual de ponto final privado

* Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnetPE'
    AddressPrefix = '11.1.0.0/24'
    PrivateEndpointNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '11.1.0.0/16'
    Subnet = $subnetConfig
}
$vnetpe = New-AzVirtualNetwork @net

```

### <a name="create-endpoint-and-connection"></a>Criar ponto final e conexão

* Utilize [o Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) para colocar a configuração do serviço de ligação privada que criou precocemente numa variável para utilização posterior.

* Utilize [o New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) para criar a configuração de ligação.

* Utilize [o New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) para criar o ponto final.



```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

## Create the private link configuration and place in variable. ##
$par2 = @{
    Name = 'myPrivateLinkConnection'
    PrivateLinkServiceId = $pls.Id
}
$plsConnection = New-AzPrivateLinkServiceConnection @par2

## Place the virtual network into a variable. ##
$par3 = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$vnetpe = Get-AzVirtualNetwork @par3

## Create private endpoint ##
$par4 = @{
    Name = 'MyPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    Subnet = $vnetpe.subnets[0]
    PrivateLinkServiceConnection = $plsConnection
}
New-AzPrivateEndpoint @par4 -ByManualRequest
```

### <a name="approve-the-private-endpoint-connection"></a>Aprovar a ligação de ponto final privado

Nesta secção, irá aprovar a ligação que criou nos passos anteriores.

* Utilize [approve-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnnection) para aprovar a ligação.

```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

$par2 = @{
    Name = $pls.PrivateEndpointConnections[0].Name
    ServiceName = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Description = 'Approved'
}
Approve-AzPrivateEndpointConnection @par2

```

### <a name="ip-address-of-private-endpoint"></a>Endereço IP do ponto final privado

Nesta secção, encontrará o endereço IP do ponto final privado que corresponde ao equilibrador de carga e ao serviço de ligação privada.

* Utilize [o Get-AzPrivateEndpoint](/powershell/module/az.network/get-azprivateendpoint) para recuperar o endereço IP.

```azurepowershell-interactive
## Get private endpoint and the IP address and place in a variable for display. ##
$par1 = @{
    Name = 'myPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    ExpandResource = 'networkinterfaces'
}
$pe = Get-AzPrivateEndpoint @par1

## Display the IP address by expanding the variable. ##
$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
```

```bash
❯ $pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
11.1.0.4
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o equilibrador de carga e os recursos restantes.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePrivLinkService-rg'
```

## <a name="next-steps"></a>Próximos passos

Neste início rápido, irá:

* Criei uma rede virtual e um Equilibrador de Carga Azure interno.
* Criei um serviço de ligação privada

Para saber mais sobre o ponto final da Azure Private, continue a:
> [!div class="nextstepaction"]
> [Quickstart: Criar um ponto final privado usando a azure powershell](create-private-endpoint-powershell.md)

