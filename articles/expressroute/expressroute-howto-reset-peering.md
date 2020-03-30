---
title: 'Azure ExpressRoute: Reset circuit peering'
description: Como desativar e ativar os pares do circuito ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941745"
---
# <a name="reset-expressroute-circuit-peerings"></a>Redefinir os pares do circuito ExpressRoute

Este artigo descreve como desativar e ativar os pares de um circuito ExpressRoute usando powerShell. Quando desativar um olhar, a sessão de BGP tanto na ligação primária como na ligação secundária do circuito ExpressRoute será desligada. Perderá a conectividade através deste olhar para a Microsoft. Quando ativar um olhar, a sessão de BGP tanto na ligação primária como na ligação secundária do circuito ExpressRoute será trazida. Você vai recuperar a conectividade através deste olhar para a Microsoft. Pode ativar e desativar o Microsoft Peering e o Azure Private Peering num circuito ExpressRoute de forma independente. Quando configurar os pares no circuito ExpressRoute, os pares são ativados por padrão.

Existem alguns cenários em que poderá achar útil redefinir os seus pares ExpressRoute.
* Teste o seu design e implementação de recuperação de desastres. Por exemplo, tem dois circuitos ExpressRoute. Pode desativar os pinos de um circuito e forçar o tráfego da rede a falhar no outro circuito.
* Ative a Deteção de Reencaminhamento Bidirecional (BFD) no Azure Private Peering ou no Microsoft Peering do seu circuito ExpressRoute. O BFD é ativado por padrão no Azure Private Peering se o seu circuito ExpressRoute for criado após 1 de agosto de 2018 e no Microsoft Peering se o seu circuito ExpressRoute for criado após 10 de janeiro de 2020. Se o seu circuito foi criado antes disso, o BFD não estava ativado. Pode ativar o BFD desativando o epeering e reativando-o. 

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Redefinir um olhar

1. Se estiver a executar o PowerShell localmente, abra a consola PowerShell com privilégios elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

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
4. Execute os seguintes comandos para recuperar o circuito ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifique o epeering que pretende desativar ou ativar. *Olhares* é uma matriz. No exemplo seguinte, Peerings[0] é Azure Private Peering e Peerings[1] Microsoft Peering.

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
6. Executar as seguintes ordens para mudar o estado do peering.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   O olhar deve estar num estado que definiu. 

## <a name="next-steps"></a>Passos seguintes
Se precisar de ajuda para resolver um problema da ExpressRoute, consulte os seguintes artigos:
* [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Resolver problemas de desempenho da rede](expressroute-troubleshooting-network-performance.md)
