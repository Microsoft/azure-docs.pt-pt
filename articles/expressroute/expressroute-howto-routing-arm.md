---
title: 'Configurar o emparelhamento para um circuito-ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 813263442bc82254d0cb5ea9e9f7e8a265de5b4a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748213"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Criar e modificar o emparelhamento para um circuito do ExpressRoute usando o PowerShell

Este artigo ajuda você a criar e gerenciar a configuração de roteamento de um circuito do ExpressRoute no modelo de implantação do Gerenciador de recursos usando o PowerShell. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos para um circuito do ExpressRoute. Se você quiser usar um método diferente para trabalhar com o circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo-emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – emparelhamento público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo-emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 


Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IPVPN, como o MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Verifique com seu provedor de serviços antes de configurar emparelhamentos de BGP.
> 
> 

Pode configurar um, dois ou todos os três peerings (Azure privado, Azure público e Microsoft) para um circuito ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de roteamento e emparelhamentos, consulte [domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito do ExpressRoute deve estar em um estado provisionado e habilitado para que você possa executar os cmdlets neste artigo.

### <a name="working-with-azure-powershell"></a>Trabalhando com Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute que foram configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados por meio do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos do ExpressRoute que são configurados em ou após 1º de agosto de 2017 não terão prefixos anunciados até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Inicie sessão e selecione a sua subscrição.

   Se você instalou o PowerShell localmente, entre. Se você estiver usando Azure Cloud Shell, poderá ignorar esta etapa.

   ```azurepowershell
   Connect-AzAccount
   ```

   Selecione a assinatura para a qual você deseja criar o circuito do ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute.

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. f seu provedor de conectividade oferece serviços gerenciados de camada 3, você pode solicitar que seu provedor de conectividade habilite o emparelhamento da Microsoft para você. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, continue sua configuração usando as próximas etapas. 

3. Verifique o circuito do ExpressRoute para certificar-se de que ele está provisionado e também habilitado. Utilize o seguinte exemplo:

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

   * Uma sub-rede/30 ou/126 para o link principal. Este deve ser um prefixo IPv4 ou IPv6 público válido de sua propriedade e registrado em um RIR/TIR.
   * Uma sub-rede/30 ou/126 para o link secundário. Este deve ser um prefixo IPv4 ou IPv6 público válido de sua propriedade e registrado em um RIR/TIR.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se você planeja enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR. As sessões de BGP IPv4 exigem prefixos de IPv4 anunciados e sessões BGP de IPv6 exigem prefixos anunciados IPv6. 
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

   Use o exemplo a seguir para configurar o emparelhamento da Microsoft para seu circuito:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="getmsft"></a>Para obter detalhes de emparelhamento da Microsoft

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Para atualizar a configuração de emparelhamento da Microsoft

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>Para excluir o emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

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

   Você também pode apenas importar um módulo Select dentro do intervalo de versão semântica conhecido.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Entre em sua conta.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selecione a assinatura para a qual você deseja criar o circuito do ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute.

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. Se seu provedor de conectividade oferece serviços gerenciados de camada 3, você pode pedir que seu provedor de conectividade habilite o emparelhamento privado do Azure para você. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, continue sua configuração usando as próximas etapas.

3. Verifique o circuito do ExpressRoute para certificar-se de que ele está provisionado e também habilitado. Utilize o seguinte exemplo:

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

   * Uma sub-rede /30 para a ligação primária. A sub-rede não deve fazer parte de nenhum espaço de endereço reservado para redes virtuais.
   * Uma sub-rede /30 para a ligação secundária. A sub-rede não deve fazer parte de nenhum espaço de endereço reservado para redes virtuais.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Assegure que não está a utilizar 65515.
   * Opcional:
     * Um hash MD5 se optar por utilizar um.

   Use o exemplo a seguir para configurar o emparelhamento privado do Azure para seu circuito:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
   > 
   >

### <a name="getprivate"></a>Para obter detalhes de emparelhamento privado do Azure

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Para atualizar a configuração de emparelhamento privado do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID de VLAN do circuito está sendo atualizada de 100 para 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Para excluir o emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

> [!WARNING]
> Você deve garantir que todas as redes virtuais e conexões de Alcance Global ExpressRoute sejam removidas antes de executar este exemplo. 
> 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Emparelhamento público do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento público do Azure para um circuito do ExpressRoute.

> [!Note]
> O emparelhamento público do Azure foi preterido para novos circuitos. Para obter mais informações, consulte [emparelhamento de ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure

1. Importe o módulo do PowerShell para ExpressRoute.

   Instale o programa de instalação mais recente do PowerShell a partir da [Galeria do PowerShell](https://www.powershellgallery.com/) e importe os módulos do Azure Resource Manager para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como Administrador.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importe todos os módulos AZ.\* dentro do intervalo de versão semântica conhecido.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Você também pode apenas importar um módulo Select dentro do intervalo de versão semântica conhecido.

   ```azurepowershell-interactive
   Import-Module Az.Network
   ```

   Entre em sua conta.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selecione a assinatura para a qual você deseja criar o circuito do ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute.

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. Se seu provedor de conectividade oferece serviços gerenciados de camada 3, você pode pedir que seu provedor de conectividade habilite o emparelhamento público do Azure para você. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, continue sua configuração usando as próximas etapas.

3. Verifique o circuito do ExpressRoute para garantir que ele seja provisionado e também habilitado. Utilize o seguinte exemplo:

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
4. Configure o peering público do Azure para o circuito. Verifique se você tem as seguintes informações antes de prosseguir.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Opcional:
     * Um hash MD5 se optar por utilizar um.

   Execute o exemplo a seguir para configurar o emparelhamento público do Azure para seu circuito

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
   > 
   >

### <a name="getpublic"></a>Para obter detalhes de emparelhamento público do Azure

Você pode obter detalhes de configuração usando o seguinte cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Para atualizar a configuração de emparelhamento público do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID de VLAN do circuito está sendo atualizada de 200 para 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Para excluir o emparelhamento público do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Passos seguintes

Passo seguinte, [Ligar uma VNet a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
