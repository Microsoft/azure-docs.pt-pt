---
title: Create an Azure private link service using Azure PowerShell| Microsoft Docs
description: Learn how to create an Azure private link service using Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: c5910649127c4b2e78cfc12065a8f12a41f7309a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229381"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Create a Private Link service using Azure PowerShell
This article shows you how to create a private link service in Azure using Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you choose to install and use PowerShell locally, this article requires the latest Azure PowerShell module version. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Before you can create your private link, you must create a resource group with [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). The following example creates a resource group named *myResourceGroup* in the *WestCentralUS* location:

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Criar rede virtual
Create a virtual network for your private link with [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). The following example creates a virtual network named *myvnet* with subnet for frontend (*frontendSubnet*), backend (*backendSubnet*), private link (*otherSubnet*):

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
## <a name="create-internal-load-balancer"></a>Create Internal Load Balancer
Create an internal Standard Load Balancer with [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). The following example creates an internal Standard Load Balancer using the frontend IP configuration, probe, rule and backend pool  that you created in the preceding steps:

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
## <a name="create-a-private-link-service"></a>Create a private link service
Create a private link service with [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  This example creates a private link service named *myPLS* using Standard Load Balancer in resource group named *myResourceGroup*. 
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

### <a name="get-private-link-service"></a>Get private link service
Get details about your private link service with [New-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) as follows:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

At this stage, your Private Link Service is successfully created and is ready to receive the traffic. Note that above example is only to demonstrate creating Private Link Service using PowerShell.  We haven't configured the load balancer backend pools or any application on the backend pools to listen to the traffic. If you want to see end to end traffic flows, you can strongly advised to configure your application behind your standard load balancer. 

Next we will demonstrate how to map this service to a private endpoint in different VNet using PowerShell. Again, the example is limited to creating the Private Endpoint and connecting to Private Link Service created above. You can create Virtual Machines in the Virtual Network to send/receive traffic to the private endpoint for building your scenario. 

## <a name="create-a-private-endpoint"></a>Criar um Ponto Final Privado
### <a name="create-a-virtual-network"></a>Criar rede virtual
Create a virtual network for your private endpoint with [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). This example creates a virtual network named *vnetPE* in resource group named *myResourceGroup*:
 
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

### <a name="create-a-private-endpoint"></a>Create a private endpoint
Create a private endpoint for consuming private link service created above in your virtual network:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Get private endpoint
Get the IP address of the private endpoint with `Get-AzPrivateEndpoint` as follows:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Approve the private endpoint connection
Approve the private end point connection to the private link service with 'Approve-AzPrivateEndpointConnection`.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Passos seguintes
- Learn more about [Azure private link](private-link-overview.md)
 
