---
title: 'Azure ExpressRoute: configurar o emparelhamento: PowerShell'
description: Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 2c28df35eec862afb5b0078ca7693898e9b58533
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436949"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Criar e modificar um peering de um circuito do ExpressRoute com o PowerShell

Este artigo ajuda-o a criar e gerir a configuração de encaminhamento de um circuito do ExpressRoute no modelo de implementação do Resource Manager com o PowerShell. Também pode verificar o estado, update ou delete e desaprovisionar peerings para um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com o seu circuito, selecione um artigo da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Emparelhamento público](about-public-peering.md)
> * [Vídeo - peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - peering da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 


Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade irá configurar e gerir encaminhamento por si.

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Contacte o seu fornecedor de serviços antes de configurar os peerings BGP.
> 
> 

Você pode configurar emparelhamento privado e emparelhamento da Microsoft para um circuito do ExpressRoute (o emparelhamento público do Azure é preterido para novos circuitos). Os emparelhamentos podem ser configurados em qualquer ordem escolhida. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e peerings, consulte [domínios de encaminhamento do ExpressRoute](expressroute-circuit-peerings.md). Para obter informações sobre o emparelhamento público, consulte [emparelhamento público do ExpressRoute](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito do ExpressRoute tem de ser num Estado aprovisionado e ativado para que possa ser capaz de executar os cmdlets neste artigo.

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="msft"></a>Peering da Microsoft

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de peering da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> Peering da Microsoft dos circuitos do ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através de peering, da Microsoft, mesmo se os filtros de rota não estão definidos. Peering da Microsoft dos circuitos do ExpressRoute que estão configurados em ou depois de 1 de Agosto de 2017 não terão qualquer prefixos anunciados até que um filtro de rota é anexado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para peering da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Inicie sessão e selecione a sua subscrição.

   Se tiver instalado o PowerShell localmente, de início de sessão. Se estiver a utilizar o Azure Cloud Shell, pode ignorar este passo.

   ```azurepowershell
   Connect-AzAccount
   ```

   Selecione a subscrição que pretende criar o circuito do ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute.

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. f o seu fornecedor de conectividade oferece serviços geridos de camada 3, pode pedir ao seu fornecedor de conectividade para ativar o peering da Microsoft para. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, continue a configuração com os passos seguintes. 

3. Verifique o circuito de ExpressRoute para se certificar-se de que é aprovisionado e também ativado. Utilize o seguinte exemplo:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

> [!IMPORTANT]
> A Microsoft verifica se os ' prefixos públicos publicados ' e ' peer ASN ' (ou ' cliente ASN ') especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver obtendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá ' AdvertisedPublicPrefixesState ' como ' validação necessária ' na saída de "Get-AzExpressRouteCircuitPeeringConfig" (consulte o comando "para obter detalhes de emparelhamento da Microsoft" abaixo). 
> 
> Se você vir a mensagem "validação necessária", colete os documentos que mostram os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registro de roteamento e envie esses documentos para validação manual por abrindo um tíquete de suporte, conforme mostrado abaixo. 
> 
>

   Utilize o exemplo seguinte para configurar o peering da Microsoft para o seu circuito:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="getmsft"></a>Para obter os detalhes do peering Microsoft

Pode obter detalhes de configuração com o exemplo seguinte:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Para atualizar a configuração do peering da Microsoft

Pode atualizar qualquer parte da configuração com o exemplo seguinte:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>A eliminar peering da Microsoft

Pode remover a sua configuração de peering executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Importe o módulo do PowerShell para ExpressRoute.

   Instale o programa de instalação mais recente do PowerShell a partir da [Galeria do PowerShell](https://www.powershellgallery.com/) e importe os módulos do Azure Resource Manager para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como Administrador.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importe todos os módulos AZ.\* dentro do intervalo de versão semântica conhecido.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Pode também simplesmente importar um módulo de seleção dentro do intervalo da versão semântica conhecida.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Inicie sessão na sua conta.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selecione a subscrição que pretende criar o circuito do ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute.

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de camada 3, pode fazer com que o seu fornecedor de conectividade para ativar o peering privado do Azure para. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gere o encaminhamento por si, depois de criar o seu circuito, continue a configuração com os passos seguintes.

3. Verifique o circuito de ExpressRoute para se certificar-se de que é aprovisionado e também ativado. Utilize o seguinte exemplo:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se optar por utilizar um hash MD5, utilize o seguinte exemplo:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
   > 
   >

### <a name="getprivate"></a>Para obter detalhes de peering privados do Azure

Para obter detalhes de configuração através do exemplo seguinte:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Para atualizar a configuração de peering privado do Azure

Pode atualizar qualquer parte da configuração com o exemplo seguinte. Neste exemplo, o ID de VLAN do circuito está a ser atualizado de 100 para 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Para eliminar um peering privado do Azure

Pode remover a sua configuração de peering executando o seguinte exemplo:

> [!WARNING]
> Tem de se certificar de que todas as ligações de alcance Global do ExpressRoute e de redes virtuais são removidas antes de executar este exemplo. 
> 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


## <a name="next-steps"></a>Passos seguintes

Passo seguinte, [Ligar uma VNet a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
