---
title: 'Como configurar o encaminhamento de circuito (peering) para o ExpressRoute: Resource Manager: PowerShell: Azure | Documentos da Microsoft'
description: Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
documentationcenter: na
services: expressroute
author: osamazia
manager: jonor
editor: ''
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2018
ms.author: osamaz, jaredr80
ms.openlocfilehash: dc67f4a4e2189a63cfd4adbb5c1b7eace23acad5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957535"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Criar e modificar um peering de um circuito do ExpressRoute com o PowerShell

Este artigo ajuda-o a criar e gerir a configuração de encaminhamento de um circuito do ExpressRoute no modelo de implementação do Resource Manager com o PowerShell. Também pode verificar o estado, update ou delete e desaprovisionar peerings para um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com o seu circuito, selecione um artigo da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo - peering privado](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - peering público](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo - peering da Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Terá da versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). 
* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito do ExpressRoute tem de ser num Estado aprovisionado e ativado para que possa ser capaz de executar os cmdlets neste artigo.

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade irá configurar e gerir encaminhamento por si.

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Contacte o seu fornecedor de serviços antes de configurar os peerings BGP.
> 
> 

Pode configurar um, dois ou todos os três peerings (Azure privado, Azure público e Microsoft) para um circuito ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e peerings, consulte [domínios de encaminhamento do ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Peering da Microsoft

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de peering da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> Peering da Microsoft dos circuitos do ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através de peering, da Microsoft, mesmo se os filtros de rota não estão definidos. Peering da Microsoft dos circuitos do ExpressRoute que estão configurados em ou depois de 1 de Agosto de 2017 não terão qualquer prefixos anunciados até que um filtro de rota é anexado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para peering da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Importe o módulo do PowerShell para ExpressRoute.

  Instale o programa de instalação mais recente do PowerShell a partir da [Galeria do PowerShell](http://www.powershellgallery.com/) e importe os módulos do Azure Resource Manager para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como Administrador.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
  ```

  Importe todos os módulos azurerm. * dentro do intervalo da versão semântica conhecida.

  ```powershell
  Import-AzureRM
  ```

  Pode também simplesmente importar um módulo de seleção dentro do intervalo da versão semântica conhecida.

  ```powershell
  Import-Module AzureRM.Network
  ```

  Inicie sessão na sua conta.

  ```powershell
  Connect-AzureRmAccount
  ```

  Selecione a subscrição que pretende criar o circuito do ExpressRoute.

  ```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Crie um circuito ExpressRoute.

  Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. f o seu fornecedor de conectividade oferece serviços geridos de camada 3, pode pedir ao seu fornecedor de conectividade para ativar o peering da Microsoft para. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, continue a configuração com os passos seguintes. 

3. Verifique o circuito de ExpressRoute para se certificar-se de que é aprovisionado e também ativado. Utilize o seguinte exemplo:

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  A resposta é semelhante ao seguinte exemplo:

  ```
  Name                             : ExpressRouteARMCircuit
  ResourceGroupName                : ExpressRouteResourceGroup
  Location                         : westus
  Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
  Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
  ServiceKey                       : **************************************
  Peerings                         : []
  ```
4. Configure o peering da Microsoft para o circuito. Confirme que tem as seguintes informações antes de continuar.

  * Um /30 ou/126 sub-rede para a ligação primária. Tem de ser um público IPv4 ou IPv6 prefixo válido detido por si e registado num RIR / TIR.
  * Um /30 ou/126 sub-rede para a ligação secundária. Tem de ser um público IPv4 ou IPv6 prefixo válido detido por si e registado num RIR / TIR.
  * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
  * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
  * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR. Sessões de BGP de IPv4 necessitam de que IPv4 anunciados prefixos e sessões de BGP de IPv6 exigem que prefixos anunciados do IPv6. 
  * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
  * Opcional:
    * Cliente ASN: se estiver a anunciar prefixos que não estão registados para o número AS de peering, pode especificar o número AS no qual estão registados.
    * Um hash MD5 se optar por utilizar um.

  Utilize o exemplo seguinte para configurar o peering da Microsoft para o seu circuito:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

### <a name="getmsft"></a>Para obter os detalhes do peering Microsoft

Pode obter detalhes de configuração com o exemplo seguinte:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Para atualizar a configuração do peering da Microsoft

Pode atualizar qualquer parte da configuração com o exemplo seguinte:

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>A eliminar peering da Microsoft

Pode remover a sua configuração de peering executando o seguinte cmdlet:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Importe o módulo do PowerShell para ExpressRoute.

  Instale o programa de instalação mais recente do PowerShell a partir da [Galeria do PowerShell](http://www.powershellgallery.com/) e importe os módulos do Azure Resource Manager para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como Administrador.

  ```powershell
  Install-Module AzureRM
  Install-AzureRM
  ```

  Importe todos os módulos azurerm. * dentro do intervalo da versão semântica conhecida.

  ```powershell
  Import-AzureRM
  ```

  Pode também simplesmente importar um módulo de seleção dentro do intervalo da versão semântica conhecida.

  ```powershell
  Import-Module AzureRM.Network 
  ```

  Inicie sessão na sua conta.

  ```powershell
  Connect-AzureRmAccount
  ```

  Selecione a subscrição que pretende criar o circuito do ExpressRoute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Crie um circuito ExpressRoute.

  Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering privado do Azure para. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, continue a configuração com os passos seguintes.

3. Verifique o circuito de ExpressRoute para se certificar-se de que é aprovisionado e também ativado. Utilize o seguinte exemplo:

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  A resposta é semelhante ao seguinte exemplo:

  ```
  Name                             : ExpressRouteARMCircuit
  ResourceGroupName                : ExpressRouteResourceGroup
  Location                         : westus
  Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
  Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
  ServiceKey                       : **************************************
  Peerings                         : []
  ```
4. Configure o peering privado do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

  * Uma sub-rede /30 para a ligação primária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais.
  * Uma sub-rede /30 para a ligação secundária. A sub-rede não pode ser parte de qualquer espaço de endereço reservado para redes virtuais.
  * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
  * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Assegure que não está a utilizar 65515.
  * Opcional:
    * Um hash MD5 se optar por utilizar um.

  Utilize o exemplo seguinte para configurar o peering privado do Azure para o seu circuito:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Se optar por utilizar um hash MD5, utilize o seguinte exemplo:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
  > 
  >

### <a name="getprivate"></a>Para obter detalhes de peering privados do Azure

Para obter detalhes de configuração através do exemplo seguinte:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Para atualizar a configuração de peering privado do Azure

Pode atualizar qualquer parte da configuração com o exemplo seguinte. Neste exemplo, o ID de VLAN do circuito está a ser atualizado de 100 para 500.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Para eliminar um peering privado do Azure

Pode remover a sua configuração de peering executando o seguinte exemplo:

> [!WARNING]
> Tem de se certificar de que todas as ligações de alcance Global do ExpressRoute e de redes virtuais são removidas antes de executar este exemplo. 
> 
> 

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Peering público do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering público do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure

1. Importe o módulo do PowerShell para ExpressRoute.

  Instale o programa de instalação mais recente do PowerShell a partir da [Galeria do PowerShell](http://www.powershellgallery.com/) e importe os módulos do Azure Resource Manager para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como Administrador.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
```

  Importe todos os módulos azurerm. * dentro do intervalo da versão semântica conhecida.

  ```powershell
  Import-AzureRM
  ```

  Pode também simplesmente importar um módulo de seleção dentro do intervalo da versão semântica conhecida.

  ```powershell
  Import-Module AzureRM.Network
```

  Inicie sessão na sua conta.

  ```powershell
  Connect-AzureRmAccount
  ```

  Selecione a subscrição que pretende criar o circuito do ExpressRoute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Crie um circuito ExpressRoute.

  Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering público do Azure para. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, continue a configuração com os passos seguintes.

3. Verifique o circuito de ExpressRoute para garantir que é aprovisionado e também ativado. Utilize o seguinte exemplo:

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  A resposta é semelhante ao seguinte exemplo:

  ```
  Name                             : ExpressRouteARMCircuit
  ResourceGroupName                : ExpressRouteResourceGroup
  Location                         : westus
  Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
  Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
  ServiceKey                       : **************************************
  Peerings                         : []
  ```
4. Configure o peering público do Azure para o circuito. Certifique-se de que tem as seguintes informações antes de prosseguir.

  * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
  * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
  * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
  * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
  * Opcional:
    * Um hash MD5 se optar por utilizar um.

  Executar o exemplo seguinte para configurar o peering público do Azure para o seu circuito

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Se optar por utilizar um hash MD5, utilize o seguinte exemplo:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  > [!IMPORTANT]
  > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
  > 
  >

### <a name="getpublic"></a>Para obter detalhes de peering públicos do Azure

Pode obter detalhes de configuração com o seguinte cmdlet:

```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Para atualizar a configuração do peering público do Azure

Pode atualizar qualquer parte da configuração com o exemplo seguinte. Neste exemplo, o ID de VLAN do circuito está a ser atualizado de 200 para 600.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Para eliminar o peering público do Azure

Pode remover a sua configuração de peering executando o seguinte exemplo:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Passos Seguintes

Passo seguinte, [Ligar uma VNet a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
