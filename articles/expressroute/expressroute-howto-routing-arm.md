---
title: 'Tutorial: Configure peering para o circuito ExpressRoute - Azure PowerShell'
description: Este tutorial mostra-lhe como criar e gerir a configuração de encaminhamento para um circuito ExpressRoute no modelo de implementação do Gestor de Recursos utilizando o PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 7cfd378ae621192cd98b482b66c85c3dcd3ca454
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721944"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Tutorial: Criar e modificar o espreitamento para um circuito ExpressRoute usando PowerShell

Este tutorial ajuda-o a criar e gerir a configuração de encaminhamento para um circuito ExpressRoute no modelo de implementação do Gestor de Recursos utilizando o PowerShell. Também pode verificar o estado, a atualização ou eliminação e desprovisionamento de um circuito ExpressRoute. Se pretender utilizar um método diferente para trabalhar com o seu circuito, selecione um artigo da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Peering público](about-public-peering.md)
> * [Vídeo - Peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - Microsoft a espreitar](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos da Camada 3 (normalmente um IPVPN, como o MPLS), o seu fornecedor de conectividade configurará e gerirá o encaminhamento para si.

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Consulte o seu fornecedor de serviços antes de configurar os seus pares BGP.
> 

Pode configurar o espreitamento privado e a Microsoft a espreitar por um circuito ExpressRoute (o espremiamento público Azure é precotado para novos circuitos). Os seus pares podem ser configurados em qualquer ordem que escolherem. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e observação, consulte [os domínios de encaminhamento ExpressRoute](expressroute-circuit-peerings.md). Para obter informações sobre olhando o público, consulte [o público ExpressRoute.](about-public-peering.md)

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Configurar, atualizar e eliminar a Microsoft a espreitar por um circuito
> - Configurar, atualizar e apagar Azure private espreitar por um circuito

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que reviu as seguintes páginas antes de iniciar a configuração:
    * [Pré-requisitos](expressroute-prerequisites.md) 
    * [Requisitos do encaminhamento](expressroute-routing.md)
    * [Fluxos de trabalho](expressroute-workflows.md)
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute deve estar em estado a provisionado e habilitado para que você execute os cmdlets neste artigo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft a espreitar

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de espreitar a Microsoft para um circuito ExpressRoute.

> [!IMPORTANT]
> O perspitamento da Microsoft nos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do olho da Microsoft, mesmo que os filtros de rota não estejam definidos. O perspitamento da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para o microsoft espreitar.](how-to-routefilter-powershell.md)
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Inscreva-se e selecione a sua subscrição.

   Se instalou o PowerShell localmente, faça o seu trabalho. Se estiver a usar a Azure Cloud Shell, pode saltar este passo.

   ```azurepowershell
   Connect-AzAccount
   ```

   Selecione a subscrição desejada para criar o circuito ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute.

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos da Camada 3, pode pedir ao seu fornecedor de conectividade para ativar o seu olhar pela Microsoft. Não precisa de seguir as instruções listadas nas próximas secções. No entanto, se o seu fornecedor de conectividade não gerir o encaminhamento para si, depois de criar o seu circuito, continue a sua configuração utilizando os próximos passos. 

3. Verifique o circuito ExpressRoute para se certificar de que está a provisionado e também ativado. Utilize o seguinte exemplo:

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
4. Configure o peering da Microsoft para o circuito. Certifique-se de que tem as seguintes informações antes de continuar.

   * Uma sub-rede /30 ou /126 para a ligação primária. O bloco de endereços deve ser um prefixo IPv4 ou IPv6 válido que lhe é propriedade e registado num RIR/IRR.
   * Uma sub-rede /30 ou/126 para a ligação secundária. O bloco de endereços deve ser um prefixo IPv4 ou IPv6 válido que lhe é propriedade e registado num RIR/IRR.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: Você fornece uma lista de todos os prefixos que pretende anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgula. Estes prefixos têm de ser registados para si num RIR/TIR. As sessões de BGP IPv4 requerem prefixos anunciados IPv4 e as sessões de BGP IPv6 requerem prefixos anunciados IPv6. 
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * Opcional:
     * Cliente ASN: Se estiver a publicitar prefixos não registados no número AS, pode especificar o número AS em que estão registados.
     * Um hash MD5 se optar por utilizar um.

> [!IMPORTANT]
> A Microsoft verifica se os prefixos públicos anunciados especificados e 'Peer ASN' (ou 'Cliente ASN') são-lhe atribuídos no Registo de Encaminhamento de Internet. Se estiver a receber os prefixos públicos de outra entidade e se a atribuição não for registada com o registo de encaminhamento, a validação automática não será completa e exigirá validação manual. Se a validação automática falhar, verá 'PublicimentdPublicPrefixesState' como 'Validação necessária' na saída de "Get-AzExpressRouteCircuitPeeringConfig" (ver "Obter detalhes de peering da Microsoft" na secção seguinte). 
> 
> Se vir a mensagem 'Validação necessária', recolha os documentos(s) que mostram que os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registo de encaminhamento e submeta esses documentos para validação manual através da abertura de um bilhete de apoio. 
> 

   Utilize o seguinte exemplo para configurar o olhar da Microsoft para o seu circuito:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Para obter os detalhes do peering da Microsoft

Pode obter detalhes de configuração usando o seguinte exemplo:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Para atualizar a configuração do peering da Microsoft

Pode atualizar qualquer parte da configuração utilizando o seguinte exemplo:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de observação privada Azure para um circuito ExpressRoute.

> [!IMPORTANT]
> O apoio iPv6 ao peering privado está atualmente em **Visualização Pública**. 
> 
> 

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Importe o módulo do PowerShell para ExpressRoute.

   Instale o mais recente instalador PowerShell da [PowerShell Gallery](https://www.powershellgallery.com/). Em seguida, importe os módulos Azure Resource Manager para a sessão PowerShell para começar a utilizar os cmdlets ExpressRoute. Terás de executar o PowerShell como administrador.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importe todos os Az. \* módulos dentro da gama de versão semântica conhecida.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Também pode importar um módulo selecionado dentro da gama de versão semântica conhecida.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Inscreva-se na sua conta.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selecione a subscrição desejada para criar o circuito ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute.

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos da Camada 3, pode pedir ao seu fornecedor de conectividade para ativar o seu olhar privado Azure. Não precisa de seguir as instruções listadas nas próximas secções. No entanto, se o seu fornecedor de conectividade não gerir o encaminhamento para si, depois de criar o seu circuito, continue a sua configuração utilizando os próximos passos.

3. Verifique o circuito ExpressRoute para se certificar de que está a provisionado e também ativado. Utilize o seguinte exemplo:

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
4. Configure o peering privado do Azure para o circuito. Certifique-se de que tem os seguintes itens antes de continuar com os próximos passos:

   * Um par de sub-redes que não fazem parte de qualquer espaço de endereço reservado para redes virtuais. Uma sub-rede será usada para a ligação primária, enquanto a outra será usada para a ligação secundária. A partir de cada uma destas sub-redes, irá atribuir o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router. Tem três opções para este par de sub-redes:
       * IPv4: Duas sub-redes /30.
       * IPv6: Duas sub-redes /126.
       * Ambas: Duas sub-redes /30 e duas sub-redes /126.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Certifique-se de que não está a usar o 65515.
   * Opcional:
     * Um hash MD5 se optar por utilizar um.

   Use o seguinte exemplo para configurar o Azure private espreitar para o seu circuito:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se optar por utilizar um haxixe MD5, utilize o seguinte exemplo:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6 -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Para obter detalhes privados de observação privada de Azure

Pode obter detalhes de configuração utilizando o seguinte exemplo:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Para atualizar a configuração do peering privado do Azure

Pode atualizar qualquer parte da configuração utilizando o seguinte exemplo. Neste exemplo, o ID VLAN do circuito está a ser atualizado de 200 para 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 500

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Para eliminar o peering da Microsoft

Pode remover a sua configuração de espreguiçadamento executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Para eliminar um peering privado do Azure

Pode remover a sua configuração de espreguiçadamento executando o seguinte exemplo:

> [!WARNING]
> Deve certificar-se de que todas as redes virtuais e as ligações ExpressRoute Global Reach são removidas antes de executar este exemplo. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o Azure em privado, pode criar uma porta de entrada ExpressRoute para ligar uma rede virtual ao circuito. 

> [!div class="nextstepaction"]
> [Configurar um gateway de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
