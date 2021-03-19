---
title: 'PowerShell: Implementar ponto final privado para aplicação web com PowerShell'
description: Saiba como usar o PowerShell para implementar o Ponto Final Privado para a sua Web App
author: ericgre
ms.assetid: e1cc08d5-91cf-49d7-8d0a-c0e7bd2046ac
ms.topic: sample
ms.date: 07/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 97ded67b9f9daa2652b2740a21a4b7d6a0ac2bdd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87068265"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-powershell"></a>Crie uma aplicação de Serviço de Aplicações e implemente o Private Endpoint usando o PowerShell

Este script de amostra cria uma aplicação no Serviço de Aplicações com os seus recursos relacionados e, em seguida, implementa um Ponto Final Privado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]


## <a name="sample-script"></a>Script de exemplo

```azurepowershell-interactive
# Parameters
$sitename="mywebapp-$(Get-Random)"
$appserviceplanname="ASP-"+$sitename
$resourcegroupname="RG-"+$sitename
$VNetname="VNet-$(Get-Random)"
$location="francecentral"
$privateendpointsubnetname = "privateEndpointSubnet"

# Create a resource group.
New-AzResourceGroup -Name $resourcegroupname -Location $location

# Create an App Service plan in PremiumV2 tier.
$asp = New-AzAppServicePlan -Name $appserviceplanname `
-Location $location `
-ResourceGroupName $resourcegroupname `
-Tier PremiumV2 `
-NumberofWorkers 1 `
-WorkerSize Small

# Create a web app.
$webApp = New-AzWebApp -Name $sitename `
-Location $location `
-AppServicePlan $appserviceplanname `
-ResourceGroupName $resourcegroupname

# Create a Virtual Network with two subnets
$integrationsubnet = New-AzVirtualNetworkSubnetConfig -Name "integrationSubnet" `
-AddressPrefix "10.8.1.0/24"

$privateendpointsubnet = New-AzVirtualNetworkSubnetConfig -Name $privateendpointsubnetname `
-AddressPrefix "10.8.2.0/24" `
-PrivateEndpointNetworkPoliciesFlag Disabled

$virtualNetwork = New-AzVirtualNetwork -Name $VNetname `
-ResourceGroupName $resourcegroupname `
-Location $location -AddressPrefix "10.8.0.0/16" `
-Subnet $integrationsubnet,$privateendpointsubnet

# Configure the Private Endpoint
$privateEndPointConnection = New-AzPrivateLinkServiceConnection -Name "myPrivateEndpointconnection" `
-PrivateLinkServiceID $webApp.Id `
-GroupId sites

$subnet = $virtualNetwork | select -ExpandProperty subnets | Where-Object {$_.Name -eq $privateendpointsubnetname}

$privateEndpoint = New-AzPrivateEndpoint -Name "myPrivateEndpoint" `
-ResourceGroupName $resourcegroupname `
-Location $location `
-Subnet $subnet `
-PrivateLinkServiceConnection $privateEndPointConnection

# Configure the Private DNS zone
$dnsZone = New-AzPrivateDnsZone -Name "privatelink.azurewebsites.net" `
-ResourceGroupName $resourcegroupname

$dnsLink = New-AzPrivateDnsVirtualNetworkLink -Name "myDNSLink" `
-ResourceGroupName $resourcegroupname `
-ZoneName "privatelink.azurewebsites.net" `
-VirtualNetworkId $virtualNetwork.Id

$dnsConfig = New-AzPrivateDnsZoneConfig -Name "privatelink.azurewebsites.net" `
-PrivateDnsZoneId $dnsZone.ResourceId

$dnsZoneGroup = New-AzPrivateDnsZoneGroup -Name "myZoneGroup" `
-ResourceGroupName $resourcegroupname `
-PrivateEndpointName $privateEndpoint.Name `
-PrivateDnsZoneConfig $dnsConfig
```


## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o seguinte comando pode ser utilizado para remover o grupo de recursos, a aplicação Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação Web. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede de rede virtual. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual. |
| [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) | Cria uma configuração de ligação de serviço de ligação privada. |
| [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) | Cria um ponto final privado. |
| [Zona de New-AzPrivateDns](/powershell/module/az.privatedns/new-azprivatednszone) | Cria uma nova zona privada de DNS. |
| [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink) | Cria uma nova ligação de rede virtual dNS privada. |
| [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig) | Cria configuração de zona DNS do grupo privado DNS zone. |
| [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup) | Cria um grupo privado de zona de DNS no ponto final privado especificado. |

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).
- Amostras adicionais do Azure PowerShell para aplicações web do Azure App Service podem ser encontradas nas [amostras Azure PowerShell](../samples-powershell.md).
