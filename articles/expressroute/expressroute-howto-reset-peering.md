---
title: 'Azure ExpressRoute: redefinir emparelhamento de circuito'
description: Como desabilitar e habilitar emparelhamentos de circuito do ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.openlocfilehash: b14b8a9a2bc43c33095bf07e7fb7ebcc2d6c1ffa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769528"
---
# <a name="reset-expressroute-circuit-peerings"></a>Redefinir emparelhamentos de circuito do ExpressRoute

Este artigo descreve como desabilitar e habilitar emparelhamentos de um circuito do ExpressRoute usando o PowerShell. Quando você desabilita um emparelhamento, a sessão BGP na conexão primária e a conexão secundária do circuito do ExpressRoute serão desligadas. Você perderá a conectividade por meio desse emparelhamento para a Microsoft. Quando você habilita um emparelhamento, a sessão BGP na conexão primária e a conexão secundária do circuito do ExpressRoute serão ativadas. Você obterá conectividade por meio desse emparelhamento para a Microsoft. Você pode habilitar e desabilitar o emparelhamento da Microsoft e o emparelhamento privado do Azure em um circuito do ExpressRoute de forma independente. Quando você configura pela primeira vez os emparelhamentos no circuito do ExpressRoute, os emparelhamentos são habilitados por padrão.

Há alguns cenários em que você pode achar útil redefinir os emparelhamentos do ExpressRoute.
* Teste seu design e implementação de recuperação de desastres. Por exemplo, você tem dois circuitos de ExpressRoute. Você pode desabilitar os emparelhamentos de um circuito e forçar o failover do tráfego de rede para o outro circuito.
* Habilite a detecção de encaminhamento bidirecional (BFD) no emparelhamento privado do Azure do circuito do ExpressRoute. O BFD será habilitado por padrão se o circuito do ExpressRoute for criado após 1º de agosto de 2018. Se o circuito foi criado antes disso, o BFD não estava habilitado. Você pode habilitar o BFD desabilitando o emparelhamento e reativando-o. Deve-se observar que o BFD tem suporte somente no emparelhamento privado do Azure.

### <a name="working-with-azure-powershell"></a>Trabalhando com Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Redefinir um emparelhamento

1. Se você estiver executando o PowerShell localmente, abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Se tiver múltiplas subscrições do Azure, verifique as subscrições da conta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Especifique a subscrição que pretende utilizar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Execute os comandos a seguir para recuperar o circuito do ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifique o emparelhamento que você deseja desabilitar ou habilitar. Os *emparelhamentos* são uma matriz. No exemplo a seguir, os emparelhamentos [0] são emparelhamento privado do Azure e emparelhamentos [1] emparelhamento da Microsoft.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Execute os comandos a seguir para alterar o estado do emparelhamento.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   O emparelhamento deve estar em um estado definido por você. 

## <a name="next-steps"></a>Passos seguintes
Se precisar de ajuda para solucionar um problema de ExpressRoute, confira os seguintes artigos:
* [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Resolver problemas de desempenho da rede](expressroute-troubleshooting-network-performance.md)
