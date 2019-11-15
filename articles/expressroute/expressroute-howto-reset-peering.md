---
title: 'Azure ExpressRoute: redefinir emparelhamento de circuito'
description: Como desactivar e activar peerings de circuito do ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: charwen
ms.openlocfilehash: 2810dfc4cb41dcf11eb59ce3c87e6f7d6b2d5f65
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083505"
---
# <a name="reset-expressroute-circuit-peerings"></a>Repor peerings de circuito do ExpressRoute

Este artigo descreve como desactivar e activar peerings de circuito do ExpressRoute com o PowerShell. Quando desativa um peering, a sessão de BGP sobre a ligação primária e a ligação secundária do seu circuito do ExpressRoute será encerrada. Perderá conectividade através deste peering para a Microsoft. Quando ativa um peering, irá ser recuperada a sessão de BGP sobre a ligação primária e a ligação secundária do seu circuito do ExpressRoute. Irá recuperar a conectividade através deste peering para a Microsoft. Pode ativar e desativar o Peering da Microsoft e o Peering privado do Azure num circuito do ExpressRoute independentemente. Quando o primeiro de configurar os peerings no seu circuito do ExpressRoute, os peerings estão ativados por predefinição.

Existem alguns cenários em que talvez ache útil a repor o seu peerings do ExpressRoute.
* Teste seu design de recuperação após desastre e implementação. Por exemplo, tem dois circuitos do ExpressRoute. Pode desativar os peerings de um circuito e forçar o tráfego de rede para a ativação pós-falha para o outro circuito.
* Habilite a detecção de encaminhamento bidirecional (BFD) no emparelhamento privado do Azure ou no emparelhamento da Microsoft do circuito do ExpressRoute. O BFD será habilitado por padrão no emparelhamento privado do Azure se o circuito do ExpressRoute for criado após 1 2018 de agosto e no emparelhamento da Microsoft se o circuito do ExpressRoute for criado após 1 2019 de outubro. Se o seu circuito foi criado antes disso, BFD não foi ativada. Pode ativar BFD desativando o peering e reativar a ele. 

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Repor um peering

1. Se estiver a executar o PowerShell localmente, abra a consola do PowerShell com privilégios elevados e ligue à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

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
4. Execute os seguintes comandos para recuperar o seu circuito do ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifique o peering que pretende desactivar ou activar. *Peerings* é uma matriz. No exemplo a seguir, Peerings [0] é o Peering privado do Azure e Peering da Microsoft Peerings [1].

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
6. Execute os seguintes comandos para alterar o estado do peering.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   O peering deve estar num Estado que definir. 

## <a name="next-steps"></a>Passos seguintes
Se precisar de ajuda para resolver um problema do ExpressRoute, veja os artigos seguintes:
* [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Resolver problemas de desempenho da rede](expressroute-troubleshooting-network-performance.md)
