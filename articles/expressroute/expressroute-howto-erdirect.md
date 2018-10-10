---
title: Como configurar o Azure ExpressRoute Direct | Documentos da Microsoft
description: Esta página ajuda-o a configurar o ExpressRoute Direct (pré-visualização)
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: e0009791263c45e0172abcb4836aaadde26f3ace
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887194"
---
# <a name="how-to-configure-expressroute-direct-preview"></a>Como configurar o ExpressRoute Direct (pré-visualização)

ExpressRoute Direct dá-lhe a capacidade de ligar diretamente para a rede global da Microsoft em localizações de peering estrategicamente distribuídas em todo o mundo. Para obter mais informações, consulte [sobre o ExpressRoute direto ligar](expressroute-erdirect-about.md).

> [!IMPORTANT]
> ExpressRoute direto está atualmente em pré-visualização.
>
> Esta Pré-visualização Pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

## <a name="resources"></a>1. Criar o recurso

1. Inicie sessão no Azure e selecione a subscrição. O recurso direto do ExpressRoute e circuitos do ExpressRoute tem de ser na mesma subscrição.

  ```powershell
  Connect-AzureRMAccount 

  Select-AzureRMSubscription -Subscription “<SubscriptionID or SubscriptionName>”
  ```
2. Liste todas as localizações onde ExpressRoute direta é suportada.
  
  ```powershell
  Get-AzureRmExpressRoutePortsLocation
  ```

  **Exemplo de saída**
  
  ```powershell
  Name                : Equinix-Ashburn-DC2
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
  ProvisioningState   : Succeeded
  Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-Dallas-DA3
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
  ProvisioningState   : Succeeded
  Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : []
  ```
3. Determinar se uma localização listada acima tem largura de banda disponível

  ```powershell
  Get-AzureRMExpressRoutePortsLocations -Name "Equinix-San-Jose-SV1"
  ```

  **Exemplo de saída**

  ```powershell
  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
  ```
4. Criar um recurso direto do ExpressRoute com base na localização selecionada acima

  ExpressRoute Direct suporta QinQ e Dot1Q encapsulamento. Se for selecionado QinQ, cada circuito ExpressRoute vai ser atribuído dinamicamente uma marca de S e seja exclusivo em todo o recurso direto do ExpressRoute. Cada etiqueta-C no circuito tem de ser exclusiva no circuito, mas não em direto do ExpressRoute.  

  Se for selecionado o encapsulamento Dot1Q, tem de gerir exclusividade de etiqueta-C (VLAN) em recursos de ExpressRoute Direct inteiro.  

  > [!IMPORTANT]
  > ExpressRoute Direct só pode ser um tipo de encapsulamento. Encapsulamento não pode ser alterado após a criação direta de ExpressRoute.
  > 
 
  ```powershell 
  $ERDirect = New-AzureRMExpressRoutePort -Name $Name -ResourceGroupName -$RGName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
  ```

  > [!NOTE]
  > O atributo de encapsulamento também poderia ser definido como Dot1Q. 
  >

  **Exemplo de saída:**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
  Circuits                   : []
  ```

## <a name="state"></a>2. Alterar o estado de administrador de ligações

Este processo deve ser utilizado para conduzir um teste de camada 1, garantindo que cada ligação cruzada é corretamente corrigida em cada router principal e secundário.

1. Obter os detalhes da direta do ExpressRoute.

  ```powershell
  $ERDirect = Get-AzureRmExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  ```
2. Defina o Link para ativado. Repita este passo para definir cada ligação ativada.

  Ligações [0] é a porta primária e Links [1] é a porta secundária.

  ```powershell
  $ERDirect.Links[0].AdminState = “Enabled”
  Set-AzureRmExpressRoutePort -ExpressRoutePort $ERDirect
  $ERDirect = Get-AzureRmExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  $ERDirect.Links[1].AdminState = “Enabled”
  Set-AzureRmExpressRoutePort -ExpressRoutePort $ERDirect
  ```
  **Exemplo de saída:**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
  Circuits                   : []
  ```

Utilize o mesmo procedimento com `AdminState = “Disabled”` para ativar às portas.

## <a name="circuit"></a>3. Criar um circuito

Por predefinição, pode criar 10 circuitos na subscrição em que é o recurso direto do ExpressRoute. Isso pode ser aumentado em suporte. É responsável por controlar aprovisionado e largura de banda utilizada. Largura de banda aprovisionada é a soma de largura de banda de todos os circuitos no recurso direto do ExpressRoute e utilizada de largura de banda é a utilização física das interfaces físicas subjacentes.

Existem larguras de banda do circuito adicionais que podem ser utilizadas no ExpressRoute Direct apenas para suportar os cenários descritos acima. Estes são: 40Gbps e 100Gbps.

Circuitos do Standard ou premium podem ser criados. Circuitos padrão são incluídos no custo, enquanto os circuitos premium têm um custo com base na largura de banda selecionada. Circuitos só podem ser criados como tráfego limitado, como ilimitado, não é suportado em direto do ExpressRoute.

Crie um circuito no recurso direto do ExpressRoute.

```powershell
New-AzureRmExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 1.0 | 2.0 | 5.0 | 10.0 | 40.0 | 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
```

Incluem outras larguras de banda: 1.0, 2.0, 5.0, 10.0 e 40.0

**Exemplo de saída:**

```powershell
Name                             : ExpressRoute-Direct-ckt
ResourceGroupName                : Contoso-Direct-rg
Location                         : westcentralus
Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
Etag                             : W/"<etagnumber>"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : null
ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
BandwidthInGbps                  : 10
Stag                             : 2
ServiceKey                       : <number>
Peerings                         : []
Authorizations                   : []
AllowClassicOperations           : False
GatewayManagerEtag     
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Direct do ExpressRoute, consulte a [descrição geral](expressroute-erdirect-about.md).
