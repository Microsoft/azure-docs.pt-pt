---
title: 'Azure ExpressRoute: Configure ExpressRoute Direct'
description: Aprenda a usar o Azure PowerShell para configurar o Azure ExpressRoute Direct para ligar diretamente à rede global da Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: f54c22a0c2f7bf89d790dbd33f748446a871d224
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099952"
---
# <a name="how-to-configure-expressroute-direct"></a>Como configurar o ExpressRoute Direct

O ExpressRoute Direct dá-lhe a capacidade de se conectar diretamente à rede global da Microsoft através de localizações de observação estrategicamente distribuídas em todo o mundo. Para obter mais informações, veja [Sobre o ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a>Antes de começar

Antes de utilizar o ExpressRoute Direct, tem primeiro de inscrever a sua subscrição. Antes de utilizar o ExpressRoute Direct, tem primeiro de inscrever a sua subscrição. Para se inscrever, por favor faça o seguinte através da Azure PowerShell:
1.  Faça o súmis no Azure e selecione a subscrição que deseja inscrever.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registe a sua subscrição para Visualização Pública utilizando o seguinte comando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Uma vez inscrito, verifique se o fornecedor de recursos **Microsoft.Network** está registado na sua subscrição. O registo de um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos.

## <a name="create-the-resource"></a><a name="resources"></a>Criar o recurso

1. Inscreva-se no Azure e selecione a subscrição. Os circuitos ExpressRoute Direct e ExpressRoute devem estar na mesma subscrição.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Re-registrar a sua subscrição para Microsoft.Network para aceder à viadutportslocalização express e expressrouteport APIs.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. Lista todos os locais onde o ExpressRoute Direct é suportado.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **Saída de exemplo**
  
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
4. Determinar se uma localização listada acima tem largura de banda disponível

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **Saída de exemplo**

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
5. Criar um recurso ExpressRoute Direct baseado no local escolhido acima

   O ExpressRoute Direct suporta a encapsulação QinQ e Dot1Q. Se o QinQ for selecionado, cada circuito ExpressRoute será atribuído dinamicamente a uma S-Tag e será único em todo o recurso ExpressRoute Direct. Cada C-Tag no circuito deve ser único no circuito, mas não através do ExpressRoute Direct.  

   Se a encapsulação do Dot1Q for selecionada, deve gerir a singularidade da C-Tag (VLAN) em todo o recurso ExpressRoute Direct.  

   > [!IMPORTANT]
   > ExpressRoute Direct só pode ser um tipo de encapsulamento. A encapsulação não pode ser alterada após a criação do ExpressRoute Direct.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > O atributo encapsulamento também pode ser definido para Dot1Q. 
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

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Gerar a Carta de Autorização (LOA)

Faça referência ao recurso ExpressRoute Direct recentemente criado, insira um nome de cliente para escrever a LOA e (opcionalmente) definir um local de arquivo para armazenar o documento. Se um caminho de ficheiro não for referenciado, o documento será transferido para o diretório atual.

  ```powershell 
   New-AzExpressRoutePortLOA -ExpressRoutePort $ERDirect -CustomerName TestCustomerName -Destination "C:\Users\SampleUser\Downloads" 
   ```
 **Saída de exemplo**

   ```powershell
   Written Letter of Authorization To: C:\Users\SampleUser\Downloads\LOA.pdf
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>Alterar Estado de Administração de ligações
   
Este processo deve ser utilizado para realizar um teste da Camada 1, certificando-se de que cada ligação cruzada está corretamente remendada em cada router para o primário e secundário.
1. Obtenha detalhes diretos expressRoute.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Definir Link para Ativado. Repita este passo para definir cada ligação ativada.

   Links[0] é a porta primária e links[1] é a porta secundária.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
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

   Utilize o mesmo procedimento `AdminState = "Disabled"` para desligar as portas.

## <a name="create-a-circuit"></a><a name="circuit"></a>Criar um circuito

Por predefinição, pode criar 10 circuitos na subscrição onde se encontra o recurso ExpressRoute Direct. Este limite pode ser aumentado através do apoio. É responsável por rastrear a largura de banda de Provisioned e Used. Largura de banda aprovisionada é a soma de largura de banda de todos os circuitos no recurso ExpressRoute Direct e a largura de banda utilizada é o uso físico das interfaces físicas subjacentes.

Existem larguras de banda de circuitos adicionais que podem ser utilizadas no ExpressRoute Direct para suportar apenas os cenários acima descritos. Estas larguras de banda são de 40 Gbps e 100 Gbps.

**SkuTier** pode ser Local, Standard ou Premium.

**SkuFamily** só pode ser MedidodData. Ilimitado não é suportado no ExpressRoute Direct.

Crie um circuito no recurso ExpressRoute Direct.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Outras larguras de banda incluem: 5.0, 10.0 e 40.0

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

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o ExpressRoute Direct, consulte a [Visão Geral.](expressroute-erdirect-about.md)
