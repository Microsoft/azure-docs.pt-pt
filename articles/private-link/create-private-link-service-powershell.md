---
title: Crie um serviço de ligação privada Azure utilizando a Azure PowerShell Microsoft Docs
description: Saiba como criar um serviço de ligação privada Azure utilizando a Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 3c808623269b8fabc32134a165b964a3b0747d4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88505638"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Criar um serviço de ligação privada utilizando a Azure PowerShell
Este artigo mostra-lhe como criar um serviço de ligação privada em Azure usando a Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a mais recente versão do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar o seu link privado, tem de criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *WestCentralUS:*

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual para a sua ligação privada com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myvnet* com sub-rede para*frontend (frontendSubnet),* backend *(backendSubnet),* link privado *(otherSubnet*):

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet
```
## <a name="create-internal-load-balancer"></a>Criar esquilibrador de carga interna
Criar um balanceador de carga padrão interno com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo a seguir cria um Balanceador de Carga Padrão interno utilizando a configuração IP frontal, sonda, regra e piscina de backend que criou nos passos anteriores:

```azurepowershell

$lbBackendName = "LB-backend"
$lbFrontName = "LB-frontend"
$lbName = "lb"

#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard
```
## <a name="create-a-private-link-service"></a>Criar um serviço de ligação privada
Crie um serviço de ligação privada com [o New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  Este exemplo cria um serviço de ligação privada chamado *myPLS* usando o Balancer de Carga Padrão no grupo de recursos chamado *myResourceGroup*.
```azurepowershell

$plsIpConfigName = "PLS-ipconfig"
$plsName = "pls"
$peName = "pe"
 
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig
```

### <a name="get-private-link-service"></a>Obtenha serviço de link privado
Obtenha detalhes sobre o seu serviço de link privado com [o Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) da seguinte forma:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName
```

Nesta fase, o seu Serviço de Ligação Privada é criado com sucesso e está pronto para receber o tráfego. Note que acima do exemplo é apenas para demonstrar a criação de Private Link Service usando o PowerShell.  Não configuramos as piscinas de backend do balançador de carga ou qualquer aplicação nas piscinas de backend para ouvir o tráfego. Se quiser ver o fim dos fluxos de tráfego, é fortemente aconselhado a configurar a sua aplicação por trás do seu equilibrador de carga padrão.

Em seguida, vamos demonstrar como mapear este serviço para um ponto final privado em VNet diferente usando PowerShell. Mais uma vez, o exemplo limita-se a criar o Private Endpoint e a ligar-se ao Private Link Service criado acima. Pode criar Máquinas Virtuais na Rede Virtual para enviar/receber tráfego para o ponto final privado para construir o seu cenário.

## <a name="create-a-private-endpoint"></a>Criar um Ponto Final Privado
### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual para o seu ponto final privado com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Este exemplo cria uma rede virtual chamada *vnetPE*   em grupo de recursos chamado *myResourceGroup*:

```azurepowershell
$virtualNetworkNamePE = "vnetPE"

# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled"

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet
```

### <a name="create-a-private-endpoint"></a>Criar um ponto final privado
Crie um ponto final privado para consumir o serviço de ligação privada acima criado na sua rede virtual:

```azurepowershell

$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest
```

### <a name="get-private-endpoint"></a>Obter ponto final privado
Obtenha o endereço IP do ponto final privado com o `Get-AzPrivateEndpoint` seguinte:

```azurepowershell
# Get Private Endpoint and its IP Address
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress

```

### <a name="approve-the-private-endpoint-connection"></a>Aprovar a ligação de ponto final privado
Aprovar a ligação do ponto final privado ao serviço de ligação privada com "Approve-AzPrivateEndpointConnection".

```azurepowershell

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved"

```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o link privado Azure](private-link-overview.md)

