---
title: Criar um serviço de vínculo privado do Azure usando Azure PowerShell | Microsoft Docs
description: Saiba como criar um serviço de vínculo privado do Azure usando o Azure PowerShell
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 2f9b7b148900e827f4bfb17de1ef3cf05d8bbf10
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169162"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Criar um serviço de vínculo privado usando Azure PowerShell
Este artigo mostra como criar um serviço de vínculo privado no Azure usando Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá a versão mais recente do módulo de Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar seu link privado, você deve criar um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group no local *WestCentralUS* :

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Criar rede virtual
Crie uma rede virtual para seu link privado com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myvnet* com subnet para frontend (*frontendSubnet*), backend (*backendSubnet*), link privado (*otherSubnet*):

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
## <a name="create-internal-load-balancer"></a>Criar Load Balancer interno
Crie um Standard Load Balancer interno com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo a seguir cria um Standard Load Balancer interno usando a configuração de IP de front-end, a investigação, a regra e o pool de back-end que você criou nas etapas anteriores:

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
## <a name="create-a-private-link-service"></a>Criar um serviço de vínculo privado
Crie um serviço de vínculo privado com [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  Este exemplo cria um serviço de vínculo privado chamado *myPLS* usando Standard Load Balancer no grupo de recursos chamado *MyResource*Group. 
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

### <a name="get-private-link-service"></a>Obter serviço de vínculo privado
Obtenha detalhes sobre o serviço de link privado com [New-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) da seguinte maneira:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

Neste estágio, o serviço de vínculo privado foi criado com êxito e está pronto para receber o tráfego. Observe que o exemplo acima é apenas para demonstrar a criação do serviço de vínculo privado usando o PowerShell.  Não configuramos os pools de back-end do balanceador de carga ou qualquer aplicativo nos pools de back-end para escutar o tráfego. Se você quiser ver fluxos de tráfego de ponta a ponta, poderá configurar seu aplicativo por trás do balanceador de carga padrão. 

Em seguida, demonstraremos como mapear esse serviço para um ponto de extremidade privado em uma VNet diferente usando o PowerShell. Novamente, o exemplo é limitado à criação do ponto de extremidade privado e à conexão com o serviço de vínculo privado criado acima. Você pode criar máquinas virtuais na rede virtual para enviar/receber tráfego para o ponto de extremidade privado para criar seu cenário. 

## <a name="create-a-private-endpoint"></a>Criar um Ponto Final Privado
### <a name="create-a-virtual-network"></a>Criar rede virtual
Crie uma rede virtual para seu ponto de extremidade privado com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Este exemplo cria uma rede virtual chamada *vnetPE*@no__t 1in grupo de recursos chamado *MyResource*Group:
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName myResourceGroup `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado
Crie um ponto de extremidade privado para consumir o serviço de vínculo privado criado acima em sua rede virtual:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Obter ponto de extremidade privado
Obtenha o endereço IP do ponto de extremidade privado com `Get-AzPrivateEndpoint` da seguinte maneira:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Aprovar a conexão de ponto de extremidade particular
Aprove a conexão do ponto de extremidade particular com o serviço de link privado com ' Approve-AzPrivateEndpointConnection '.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [link privado do Azure](private-link-overview.md)
 
