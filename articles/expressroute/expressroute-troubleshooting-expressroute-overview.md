---
title: 'Azure ExpressRoute: Verifique conectividade - Guia de resolução de problemas'
description: Esta página fornece instruções sobre resolução de problemas e validação de fim para fim da conectividade de um circuito ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78330962"
---
# <a name="verifying-expressroute-connectivity"></a>Verificar a conectividade do ExpressRoute
Este artigo ajuda-o a verificar e a resolver problemas a conectividade ExpressRoute. O ExpressRoute alarga uma rede no local para a nuvem da Microsoft sobre uma ligação privada que é comumente facilitada por um fornecedor de conectividade. A conectividade ExpressRoute envolve tradicionalmente três zonas de rede distintas, da seguinte forma:

-   Rede de Cliente
-   Rede de Fornecedores
-   Microsoft Datacenter

> [!NOTE]
> No modelo de conectividade direta ExpressRoute (oferecido a largura de banda de 10/100 Gbps), os clientes podem ligar-se diretamente à porta dos routers Microsoft Enterprise Edge (MSEE). Portanto, no modelo de conectividade direta, existem apenas zonas de rede de clientes e Microsoft.
>


O objetivo deste documento é ajudar o utilizador a identificar se e onde existe um problema de conectividade. Assim, para ajudar a procurar apoio da equipa apropriada para resolver um problema. Se o suporte da Microsoft for necessário para resolver um problema, abra um bilhete de suporte com o [Microsoft Support][Support].

> [!IMPORTANT]
> Este documento destina-se a ajudar a diagnosticar e corrigir questões simples. Não se destina a ser um substituto para o suporte da Microsoft. Abra um bilhete de suporte com o [Microsoft Support][Support] se não conseguir resolver o problema utilizando as orientações fornecidas.
>
>

## <a name="overview"></a>Descrição geral
O diagrama seguinte mostra a conectividade lógica de uma rede de clientes para a rede Microsoft usando o ExpressRoute.
[![1]][1]

No diagrama anterior, os números indicam pontos-chave da rede. Estes pontos de rede são referenciados neste artigo por vezes pelo seu número associado. Dependendo do modelo de conectividade ExpressRoute--Cloud Exchange Co-location, Point-to-Point Ethernet Connection, ou Any-to-any (IPVPN)-- os pontos de rede 3 e 4 podem ser comutadores (dispositivos camada 2) ou routers (dispositivos camada 3). No modelo de conectividade direta, não existem pontos de rede 3 e 4; em vez disso, os CEs (2) estão diretamente ligados aos EEE através de fibra escura. Os principais pontos de rede ilustrados são os seguintes:

1.  Dispositivo de computação do cliente (por exemplo, um servidor ou PC)
2.  CEs: Routers de borda do cliente 
3.  PEs (face CE): Routers/switchs de borda do fornecedor que estão virados para os routers de borda do cliente. Referidos como PE-CEs neste documento.
4.  PEs (virado mSEE): Routers/interruptores de borda do fornecedor que estão virados para Os EE. Referidos como PE-MSEEs neste documento.
5.  MSEEs: Microsoft Enterprise Edge (MSEE) Routers ExpressRoute
6.  Gateway de Rede Virtual (VNet)
7.  Dispositivo computacional no Azure VNet

Se forem utilizados os modelos de Co-localização cloud Exchange, Point-to-Point Ethernet ou de conectividade direta, os CEs (2) estabelecem o luminário de BGP com MSEEs (5). 

Se for utilizado o modelo de conectividade Any-to-any (IPVPN), o PE-MSEEs (4) estabelece o luminário de BGP com MSEEs (5). Os PE-MSEEs propagam as rotas recebidas da Microsoft de volta à rede de clientes através da rede de prestadores de serviços IPVPN.

> [!NOTE]
>Para uma elevada disponibilidade, a Microsoft estabelece uma conectividade paralela totalmente redundante entre os pares MSEEs (5) e PE-MSEEs (4). Um caminho de rede paralela totalmente redundante também é encorajado entre a rede de clientes e o par PE-CEs. Para mais informações sobre elevada disponibilidade, consulte o artigo [Design para alta disponibilidade com expressRoute][HA]
>
>

Seguem-se os passos lógicos, na resolução de problemas do circuito ExpressRoute:

* [Verificar o fornecimento de circuitos e o estado](#verify-circuit-provisioning-and-state)
  
* [Validar configuração de peering](#validate-peering-configuration)
  
* [Validar a ARP](#validate-arp)
  
* [Validar o BGP e as rotas no MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Confirme o fluxo de tráfego](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Verificar o fornecimento de circuitos e o estado
O fornecimento de um circuito ExpressRoute estabelece uma ligação redundante da Camada 2 entre CEs/PE-MSEEs (2)/(4) e MSEEs (5). Para obter mais informações sobre como criar, modificar, fornecer e verificar um circuito ExpressRoute, consulte o artigo [Criar e modificar um circuito ExpressRoute][CreateCircuit].

>[!TIP]
>Uma chave de serviço identifica exclusivamente um circuito ExpressRoute. Caso necessite de assistência da Microsoft ou de um parceiro ExpressRoute para resolver um problema com um problema expressRoute, forneça a chave de serviço para identificar facilmente o circuito.
>
>

### <a name="verification-via-the-azure-portal"></a>Verificação através do portal Azure
No portal Azure, abra a lâmina do circuito ExpressRoute. Na secção ![3][3] da lâmina, os elementos essenciais expressRoute estão listados como indicado na seguinte imagem:

![4][4]    

No ExpressRoute Essentials, o estado do *circuito* indica o estado do circuito no lado da Microsoft. *O estado* do prestador indica se o circuito foi *provisionado/não provisionado* do lado do prestador de serviços. 

Para que um circuito ExpressRoute esteja operacional, o estado do *Circuito* deve ser *ativado* e o estatuto de *Fornecedor* deve ser *provisionado*.

> [!NOTE]
> Depois de configurar um circuito ExpressRoute, se o estado do *Circuito* for atingido no estado não ativado, contacte o Suporte da [Microsoft][Support]. Por outro lado, se o estatuto de *Fornecedor* for atingido em estado não previsto, contacte o seu prestador de serviços.
>
>

### <a name="verification-via-powershell"></a>Verificação via PowerShell
Para listar todos os circuitos ExpressRoute num Grupo de Recursos, utilize o seguinte comando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Se procura o nome de um grupo de recursos, pode obtê-lo listando todos os grupos de recursos da sua subscrição, utilizando o comando *Get-AzResourceGroup*
>


Para selecionar um circuito ExpressRoute específico num Grupo de Recursos, utilize o seguinte comando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Uma resposta da amostra é:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Para confirmar se um circuito ExpressRoute está operacional, preste especial atenção aos seguintes campos:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Depois de configurar um circuito ExpressRoute, se o estado do *Circuito* for atingido no estado não ativado, contacte o Suporte da [Microsoft][Support]. Por outro lado, se o estatuto de *Fornecedor* for atingido em estado não previsto, contacte o seu prestador de serviços.
>
>

## <a name="validate-peering-configuration"></a>Validar configuração de peering
Depois de o prestador de serviços ter concluído o fornecimento do circuito ExpressRoute, podem ser criadas múltiplas configurações de encaminhamento baseadas em eBGP no circuito ExpressRoute entre CEs/MSEE-PEs (2)/(4) e MSEEs (5). Cada circuito ExpressRoute pode ter: Peering privado Azure (tráfego para redes virtuais privadas em Azure), e/ou opeering da Microsoft (tráfego para pontos finais públicos de PaaS e SaaS). Para obter mais informações sobre como criar e modificar a configuração de encaminhamento, consulte o artigo [Criar e modificar o encaminhamento para um circuito ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verificação através do portal Azure

> [!NOTE]
> No modelo de conectividade IPVPN, os prestadores de serviços lidam com a responsabilidade de configurar os pares (serviços de camada 3). Neste modelo, depois de o prestador de serviços ter configurado um olhar e se o olhar estiver em branco no portal, tente refrescar a configuração do circuito utilizando o botão de atualização no portal. Esta operação retirará a configuração de encaminhamento atual do seu circuito. 
>

No portal Azure, o estado de um circuito ExpressRoute pode ser verificado sob a lâmina do circuito ExpressRoute. Na secção ![3][3] da lâmina, os pares ExpressRoute seriam listados como indicado na seguinte imagem:

![5][5]

No exemplo anterior, como se nota o peering privado azure, enquanto os pares públicos e microsoft não são provisionados. Um contexto de observação bem-sucedido teria igualmente os sub-redes primários e secundários enumerados. As subredes /30 são utilizadas para o endereço IP da interface dos EEE e dos EE/PE-MSEEs. Para os pares que são provisionados, a listagem indica também quem modificou a configuração pela última vez. 

> [!NOTE]
> Se permitir uma falha de observação, verifique se as subredes primárias e secundárias atribuídas correspondem à configuração do CE/PE-MSEE ligado. Verifique também se os *VlanId,* *AzureASN*e *PeerASN* corretos são utilizados em MSEEs e se estes valores mapeiam os utilizados no CE/PE-MSEE ligados. Se o hashing MD5 for escolhido, a chave partilhada deve ser a mesma no par MSEE e PE-MSEE/CE. A chave partilhada previamente configurada não seria apresentada por razões de segurança. Se precisar de alterar qualquer destas configurações num router MSEE, consulte a [Create e modifique o encaminhamento para um circuito ExpressRoute][CreatePeering].  
>

> [!NOTE]
> Numa sub-rede /30 atribuída para interface, a Microsoft escolherá o segundo endereço IP utilizável da subnet para a interface MSEE. Por conseguinte, certifique-se de que o primeiro endereço IP utilizável da subnet foi atribuído no ce/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Verificação via PowerShell
Para obter os detalhes de configuração de peering privado do Azure, utilize os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Uma resposta da amostra, para um epeering privado configurado com sucesso, é:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Um contexto de observação ativado com sucesso teria os prefixos de endereço primário e secundário listados. As subredes /30 são utilizadas para o endereço IP da interface dos EEE e dos EE/PE-MSEEs.

Para obter os detalhes de configuração do público azure, use os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Para obter os detalhes de configuração de peering da Microsoft, utilize os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Se um epeering não estiver configurado, haverá uma mensagem de erro. Uma resposta da amostra, quando o peering declarado (pino público azure neste exemplo) não está configurado dentro do circuito:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Se permitir uma falha de observação, verifique se as subredes primárias e secundárias atribuídas correspondem à configuração do CE/PE-MSEE ligado. Verifique também se os *VlanId,* *AzureASN*e *PeerASN* corretos são utilizados em MSEEs e se estes valores mapeiam os utilizados no CE/PE-MSEE ligados. Se o hashing MD5 for escolhido, a chave partilhada deve ser a mesma no par MSEE e PE-MSEE/CE. A chave partilhada previamente configurada não seria apresentada por razões de segurança. Se precisar de alterar qualquer destas configurações num router MSEE, consulte a [Create e modifique o encaminhamento para um circuito ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> Numa sub-rede /30 atribuída para interface, a Microsoft escolherá o segundo endereço IP utilizável da subnet para a interface MSEE. Por conseguinte, certifique-se de que o primeiro endereço IP utilizável da subnet foi atribuído no ce/PE-MSEE.
>

## <a name="validate-arp"></a>Validar a ARP

A tabela ARP fornece um mapeamento do endereço IP e do endereço MAC para um determinado olhar. A tabela ARP para um circuito ExpressRoute peering fornece as seguintes informações para cada interface (primária e secundária):
* Mapeamento do endereço IP de interface de router no local para o endereço MAC
* Mapeamento do endereço IP da interface do router ExpressRoute para o endereço MAC
* A idade das tabelas ARP de mapeamento pode ajudar a validar a configuração da camada 2 e problemas de resolução de problemas problemas de conectividade camada 2.


Consulte [obter tabelas ARP no][ARP] documento do modelo de implementação do Gestor de Recursos, para ver a tabela ARP de um peering ExpressRoute, e para como usar a informação para resolver problemas problemas de conectividade camada 2.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Validar o BGP e as rotas no MSEE

Para obter a tabela de encaminhamento do MSEE no caminho *primário* para o contexto de encaminhamento *privado,* utilize o seguinte comando:

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Uma resposta exemplo é:

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> Se o estado de um eBGP a espreitar entre um MSEE e um CE/PE-MSEE estiver em Ativo ou Inativo, verifique se as subredes de pares primários e secundários atribuídas correspondem à configuração do CE/PE-MSEE ligado. Verifique também se os *VlanId,* *AzureAsn*e *PeerAsn* corretos são utilizados em MSEEs e se estes valores mapeiam os utilizados no PE-MSEE/CE ligado. Se o hashing MD5 for escolhido, a chave partilhada deve ser a mesma no par MSEE e CE/PE-MSEE. Se precisar de alterar qualquer destas configurações num router MSEE, consulte a [Create e modifique o encaminhamento para um circuito ExpressRoute][CreatePeering].
>


> [!NOTE]
> Se determinados destinos não forem acessíveis através de um olhar, verifique a tabela de rotas dos EEE para obter o contexto de observação correspondente. Se um prefixo correspondente (pode ser IP NATed) estiver presente na tabela de encaminhamento, verifique se existem firewalls/NSG/ACLs no caminho que estão bloqueando o tráfego.
>


O exemplo que se segue mostra a resposta do comando para um epeering que não existe:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>Confirme o fluxo de tráfego
Para obter as estatísticas combinadas de tráfego primário e secundário -- bytes dentro e fora -- de um contexto de observação, use o seguinte comando:

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

Uma amostra de saída do comando é:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Uma amostra de saída do comando para um epeering inexistente é:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Passos Seguintes
Para mais informações ou ajuda, consulte os seguintes links:

- [Suporte da Microsoft][Support]
- [Criar e modificar um circuito ExpressRoute][CreateCircuit]
- [Criar e modificar o encaminhamento de um circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.pngConectividade lógica da "rota expressa"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ícone de todos os recursos"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ícone de visão geral"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Screenshot da amostra do ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Screenshot da amostra do ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





