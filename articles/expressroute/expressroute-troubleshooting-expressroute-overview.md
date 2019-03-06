---
title: 'Verificar a conectividade - guia de resolução de problemas do ExpressRoute: Azure| Microsoft Docs'
description: Esta página fornece instruções sobre solução de problemas e validar a conectividade de ponto a ponto de um circuito do ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: c4b20b4007a4824ee2dea0e1b22f1ea886218885
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453614"
---
# <a name="verifying-expressroute-connectivity"></a>Verificar a conectividade do ExpressRoute
Este artigo ajuda-o a verificar e resolver problemas de conectividade do ExpressRoute. ExpressRoute, que expande uma rede no local para a cloud da Microsoft ao longo de uma ligação privada que é facilitada por um fornecedor de conectividade, envolve as seguintes três zonas de rede distintos:

-   Rede de Cliente
-   Rede de fornecedor
-   Microsoft Datacenter

O objetivo deste documento é ajudar o utilizador identifique onde (ou mesmo) existe um problema de conectividade e em qual zona, assim, para obter a ajuda de equipe apropriados para resolver o problema. Se necessitar de suporte da Microsoft para resolver um problema, abra um pedido de suporte [Support da Microsoft][Support].

> [!IMPORTANT]
> Este documento destina-se para o ajudar a diagnosticar e corrigir problemas simples. Não se destina a ser uma substituição para o suporte da Microsoft. Abra um pedido de suporte [Support da Microsoft] [ Support] se não conseguir resolver o problema através da orientação fornecida.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
O diagrama seguinte mostra a conectividade de lógica de uma rede de cliente à rede da Microsoft através do ExpressRoute.
[![1]][1]

No diagrama anterior, os números indicam os pontos de rede essencial. Os pontos de rede são referenciados frequentemente através deste artigo pelo respetivo número associado.

Consoante o modelo de conectividade do ExpressRoute (localização conjunta do Exchange na nuvem, ligação de Ethernet ponto a ponto ou qualquer a qualquer (VPN de IP)), os pontos de rede 3 e 4 podem ser comutadores (dispositivos de camada 2). Os pontos de rede essencial ilustrados são os seguintes:

1.  Dispositivo de computação de cliente (por exemplo, um servidor ou PC)
2.  CEs: Routers de limite de cliente 
3.  PEs (voltado para o CE): Fornecedor edge routers/comutadores que está a enfrentar os routers de limite de cliente. Chamado para PE CEs neste documento.
4.  PEs (com acesso do MSEE): Fornecedor edge routers/comutadores que está a enfrentar MSEEs. Chamado de PE MSEEs neste documento.
5.  MSEEs: Routers de ExpressRoute do Microsoft Enterprise Edge (MSEE)
6.  Gateway de rede virtual (VNet)
7.  Computação de dispositivo na VNet do Azure

Se forem utilizados os modelos de conectividade de localização conjunta do Exchange na nuvem ou de ligação de Ethernet ponto a ponto, o router de limite de cliente (2) poderia estabelecer BGP peering com MSEEs (5). Pontos de rede 3 e 4 seriam ainda existe, mas um pouco transparência como dispositivos de camada 2.

Se for utilizado o modelo de conectividade qualquer a qualquer (VPN de IP), o PEs (com acesso do MSEE) (4) poderia estabelecer BGP peering com MSEEs (5). As rotas, em seguida, poderiam propagar novamente para a rede de cliente através da rede de fornecedor de serviço de VPN de IP.

>[!NOTE]
>Para elevada disponibilidade do ExpressRoute, a Microsoft requer um par redundante de sessões BGP entre os MSEEs (5) e PE-MSEEs (4). Um par redundante de caminhos de rede também é incentivado entre a rede de cliente e PE CEs. No entanto, no modelo de ligação de qualquer a qualquer (VPN de IP), um único dispositivo de CE (2) pode estar conectado a um ou mais PEs (3).
>
>

Para validar um circuito do ExpressRoute, os seguintes passos são abordados (com o ponto de rede indicado pelo número associado):
1. [Validar o aprovisionamento e o estado (5)](#validate-circuit-provisioning-and-state)
2. [Validar o ExpressRoute, pelo menos, um peering é configurado (5)](#validate-peering-configuration)
3. [Validar ARP entre a Microsoft e o serviço fornecedor (ligação entre 4 e 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Validar o BGP e rotas do MSEE (BGP entre 4 a 5 e 5 para 6 se estiver ligada uma VNet)](#validate-bgp-and-routes-on-the-msee)
5. [Verifique as estatísticas de tráfego (tráfego que passa a 5)](#check-the-traffic-statistics)

Mais validações e verificações serão adicionadas em volta a verificação de futura, mensalmente!

## <a name="validate-circuit-provisioning-and-state"></a>Validar o aprovisionamento e de estado
Independentemente do modelo de conectividade, um circuito do ExpressRoute tem de ser criado e, portanto, uma chave de serviço gerado para o aprovisionamento do circuito. Aprovisionamento de um circuito do ExpressRoute estabelece um ligações de camada 2 redundantes entre PE-MSEEs (4) e MSEEs (5). Para obter mais informações sobre como criar, modificar, aprovisionar e certifique-se de um circuito do ExpressRoute, consulte o artigo [criar e modificar um circuito do ExpressRoute][CreateCircuit].

>[!TIP]
>Uma chave de serviço identifica exclusivamente um circuito do ExpressRoute. Esta chave é necessária para a maioria dos comandos do powershell mencionadas neste documento. Além disso, se necessitar de assistência da Microsoft ou de um parceiro do ExpressRoute para solucionar um problema do ExpressRoute, forneça a chave de serviço para identificar prontamente o circuito.
>
>

### <a name="verification-via-the-azure-portal"></a>Verificação através do portal do Azure
No portal do Azure, o estado de um circuito do ExpressRoute pode ser verificado, selecionando ![2][2] no menu da barra do lado do esquerda e, em seguida, selecionar o circuito do ExpressRoute. Selecionar um ExpressRoute circuito listado em "Todos os recursos" abre o painel de circuito do ExpressRoute. Na ![3][3] seção do painel, o ExpressRoute essentials é listado, conforme mostrado na captura de ecrã seguinte:

![4][4]    

No ExpressRoute Essentials, *circuito estado* indica o estado do circuito no lado do Microsoft. *Estado do fornecedor* indica se o circuito foi *aprovisionado/não aprovisionado* no lado do fornecedor de serviços. 

Para um circuito de ExpressRoute fique operacional, o *circuito status* tem de ser *ativado* e o *estado do fornecedor* tem de ser *aprovisionado*.

>[!NOTE]
>Se o *circuito status* não é ativada, entre em contato com [Support da Microsoft][Support]. Se o *estado do fornecedor* não é aprovisionado, contacte o seu fornecedor de serviços.
>
>

### <a name="verification-via-powershell"></a>Verificação através do PowerShell
Para listar todos os circuitos de ExpressRoute num grupo de recursos, utilize o seguinte comando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Pode obter o nome do grupo de recursos através do Azure. Veja a subsecção anterior deste documento e tenha em atenção que o nome do grupo de recursos está listado na captura de ecrã de exemplo.
>
>

Para selecionar um determinado circuito de ExpressRoute num grupo de recursos, utilize o seguinte comando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Uma resposta de exemplo é:

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

Para confirmar se um circuito do ExpressRoute está operacional, preste especial atenção para os seguintes campos:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Se o *CircuitProvisioningState* não é ativada, entre em contato com [Support da Microsoft][Support]. Se o *ServiceProviderProvisioningState* não é aprovisionado, contacte o seu fornecedor de serviços.
>
>

### <a name="verification-via-powershell-classic"></a>Verificação através do PowerShell (clássico)
Para listar todos os circuitos do ExpressRoute numa subscrição, utilize o seguinte comando:

    Get-AzureDedicatedCircuit

Para selecionar um determinado circuito do ExpressRoute, utilize o seguinte comando:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Uma resposta de exemplo é:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Para confirmar se um circuito do ExpressRoute está operacional, preste especial atenção para os seguintes campos: ServiceProviderProvisioningState : Estado de aprovisionamento: Ativado

>[!NOTE]
>Se o *Status* não é ativada, entre em contato com [Support da Microsoft][Support]. Se o *ServiceProviderProvisioningState* não é aprovisionado, contacte o seu fornecedor de serviços.
>
>

## <a name="validate-peering-configuration"></a>Validar a configuração de Peering
Quando o fornecedor do serviço terminar o aprovisionamento do circuito do ExpressRoute, uma configuração de roteamento pode ser criada através de circuito ExpressRoute entre MSEE os pedidos pull (4) e MSEEs (5). Cada circuito ExpressRoute pode ter um, dois ou três contextos de encaminhamento ativados: Tráfego do Azure privado peering (para redes virtuais privadas no Azure), tráfego do Azure público peering (para endereços IP públicos no Azure) e Microsoft peering (tráfego ao Office 365 e Dynamics 365). Para obter mais informações sobre como criar e modificar a configuração do encaminhamento, consulte o artigo [criar e modificar o encaminhamento de um circuito do ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verificação através do portal do Azure

>[!NOTE]
>Se a camada 3 é fornecida pelo fornecedor de serviço e os peerings estiverem em branco no portal, atualize a configuração do circuito com o botão de atualização no portal. Esta operação irá aplicar a configuração de encaminhamento correta no seu circuito. 
>
>

No portal do Azure, o estado de um circuito do ExpressRoute pode ser verificado, selecionando ![2][2] no menu da barra do lado do esquerda e, em seguida, selecionar o circuito do ExpressRoute. Selecionar do ExpressRoute circuito listado em "Todos os recursos" teria de abrir o painel de circuito do ExpressRoute. Na ![3][3] seção do painel, o ExpressRoute essentials seriam listado conforme mostrado na captura de ecrã seguinte:

![5][5]

No exemplo anterior, como observado Azure contexto de encaminhamento de peering privado está ativado, ao passo que o peering público do Azure e os contextos de encaminhamento peering Microsoft não estão ativados. Um contexto de peering ativado com êxito também teria as sub-redes de ponto a ponto primário e secundário (necessária para BGP) listadas. O/30 sub-redes são utilizadas para o endereço IP da interface do MSEEs e PE MSEEs. 

>[!NOTE]
>Se um peering não estiver ativado, verifique se as sub-redes primárias e secundárias atribuídas corresponde à configuração no PE MSEEs. Se não, para alterar a configuração nos roteadores MSEE, consulte a [criar e modificar o encaminhamento de um circuito do ExpressRoute][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Verificação através do PowerShell
Para obter os detalhes de configuração de peering de privado do Azure, utilize os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Uma resposta de exemplo, para um foi configurado com êxito peering privado, é:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Um contexto de peering ativado com êxito teria os prefixos de endereço primário e secundário listados. O/30 sub-redes são utilizadas para o endereço IP da interface do MSEEs e PE MSEEs.

Para obter os detalhes de configuração de peering de público do Azure, utilize os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Para obter os detalhes de configuração de peering do Microsoft, utilize os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Se não estiver configurado um peering, deveria haver uma mensagem de erro. Uma resposta de exemplo, quando o declarado peering (pública do Azure peering neste exemplo) não é configurada no circuito:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Se um peering não estiver ativado, verifique se as sub-redes primárias e secundárias atribuídas coincidir com a configuração a PE-MSEE ligado. Verifique também se o correto *VlanId*, *AzureASN*, e *PeerASN* são utilizados no MSEEs e se esses valores mapeia para as utilizadas do PE-MSEE ligado. Se o hash MD5 é escolhido, a chave partilhada deve ser a mesma no par MSEE e PE MSEE. Para alterar a configuração nos routers MSEE, consulte [criar e modificar o encaminhamento para um circuito ExpressRoute] [CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Verificação através do PowerShell (clássico)
Para obter os detalhes de configuração de peering de privado do Azure, utilize o seguinte comando:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

É uma resposta de exemplo, para um peering privado foi configurado com êxito:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Um foi ativada com êxito, contexto peering teria as sub-redes de elemento de rede primária e secundária listadas. O/30 sub-redes são utilizadas para o endereço IP da interface do MSEEs e PE MSEEs.

Para obter os detalhes de configuração de peering de público do Azure, utilize os seguintes comandos:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Para obter os detalhes de configuração de peering do Microsoft, utilize os seguintes comandos:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Se o fornecedor de serviço foram definidas peerings de camada 3, definir os peerings do ExpressRoute através do portal ou PowerShell substitui as definições do fornecedor de serviço. A repor as definições de peering do fornecedor lado requer o suporte do fornecedor de serviços. Só deve modificar os peerings do ExpressRoute se for determinado que o fornecedor de serviços está a fornecer apenas a serviços de camada 2!
>
>

<p/>
>[!NOTE]
>Se um peering não estiver ativado, verifique se as sub-redes do elemento de rede primária e secundária atribuídas coincidir com a configuração a PE-MSEE ligado. Verifique também se o correto *VlanId*, *AzureAsn*, e *PeerAsn* são utilizados no MSEEs e se esses valores mapeia para as utilizadas do PE-MSEE ligado. Para alterar a configuração nos routers MSEE, consulte [criar e modificar o encaminhamento para um circuito ExpressRoute] [CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Validar ARP entre a Microsoft e o fornecedor de serviços
Esta secção utiliza comandos do PowerShell (clássico). Se usa comandos do PowerShell do Azure Resource Manager, certifique-se de que tem acesso de administrador/coadministrador na subscrição. Para resolução de problemas com o Azure Resource Manager comandos, consulte a [tabelas de introdução ARP no modelo de implementação do Resource Manager] [ ARP] documento.

>[!NOTE]
>Para obter ARP, o portal do Azure e os comandos do Azure Resource Manager PowerShell podem ser utilizados. Se forem encontrados erros com os comandos do PowerShell do Azure Resource Manager, os comandos do PowerShell clássicos devem trabalhar como PowerShell clássico comandos também funcionam com os circuitos do ExpressRoute do Azure Resource Manager.
>
>

Para obter a tabela de ARP do router MSEE primário no peering privado, utilize o seguinte comando:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Uma resposta de exemplo para o comando, no cenário com êxito:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Da mesma forma, pode verificar a tabela de ARP do MSEE no *primário*/*secundário* caminho, para *privada*/*pública*  / *Microsoft* peerings.

O exemplo seguinte mostra que a resposta do comando para um peering não existe.

    ARP Info:
       
>[!NOTE]
>Se a tabela de ARP não tem endereços IP das interfaces mapeados para os endereços MAC, reveja as seguintes informações:
>1. Se o primeiro endereço IP de/30 sub-rede atribuída para a ligação entre o MSEE-PR e MSEE é utilizada na interface do MSEE-Pr. O Azure utiliza sempre o segundo endereço IP para MSEEs.
>2. Certifique-se de que se o cliente (C-etiqueta) e etiquetas de VLAN de serviço (S-etiqueta) coincidir ambos num par de MSEE-PR e MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Validar o BGP e rotas do MSEE
Esta secção utiliza comandos do PowerShell (clássico). Se usa comandos do PowerShell do Azure Resource Manager, certifique-se de que tem acesso de administrador/coadministrador na subscrição.

>[!NOTE]
>Para obter informações do BGP, o portal do Azure e os comandos do Azure Resource Manager PowerShell podem ser utilizados. Se forem encontrados erros com os comandos do PowerShell do Azure Resource Manager, os comandos do PowerShell clássicos devem trabalhar como PowerShell clássico comandos também funcionam com os circuitos do ExpressRoute do Azure Resource Manager.
>
>

Para obter a tabela de encaminhamento (vizinhos de BGP) resumo para um contexto de encaminhamento específico, utilize o seguinte comando:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Uma resposta de exemplo é:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Conforme mostrado no exemplo anterior, o comando é útil para determinar quanto ao contexto de encaminhamento de tiver sido estabelecida. Ele também indica o número de prefixos de rotas anunciadas pelo peering router.

>[!NOTE]
>Se o estado estiver ativo ou inativo, verifique se as sub-redes do elemento de rede primária e secundária atribuídas coincidir com a configuração a PE-MSEE ligado. Verifique também se o correto *VlanId*, *AzureAsn*, e *PeerAsn* são utilizados no MSEEs e se esses valores mapeia para as utilizadas do PE-MSEE ligado. Se o hash MD5 é escolhido, a chave partilhada deve ser a mesma no par MSEE e PE MSEE. Para alterar a configuração nos routers MSEE, consulte [criar e modificar o encaminhamento de um circuito do ExpressRoute][CreatePeering].
>
>

<p/>
>[!NOTE]
>Se determinados destinos não estão acessíveis através de um peering particular, verifique a tabela de rotas de MSEEs que pertencem ao contexto de peering específico. Se um prefixo correspondente (pode ser NATed IP) está presente na tabela de roteamento, verifique se existem firewalls/NSG/ACLs no caminho e se eles permitem o tráfego.
>
>

Para obter a tabela de encaminhamento completa do MSEE sobre o *primário* caminho para o determinado *privada* encaminhamento contexto, utilize o seguinte comando:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Um resultado bem-sucedido de exemplo para o comando é:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Da mesma forma, pode verificar a tabela de encaminhamento do MSEE no *primário*/*secundário* caminho, para *privada* /  *Público*/*Microsoft* um contexto de peering.

O exemplo seguinte mostra que a resposta do comando para um peering não existe:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Verifique as estatísticas de tráfego
Para obter as estatísticas de tráfego do caminho de primário e secundário combinados – bytes entrada e saída, de um contexto de peering, utilizam o seguinte comando:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Uma saída de exemplo do comando é:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

É uma saída de exemplo do comando para um peering não existente:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações ou ajuda, veja as ligações seguintes:

- [Suporte da Microsoft][Support]
- [Criar e modificar um circuito do ExpressRoute][CreateCircuit]
- [Criar e modificar o encaminhamento de um circuito do ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "conectividade do Expressroute lógico"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ícone de todos os recursos"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ícone de descrição geral"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Captura de ecrã de exemplo de Essentials do ExpressRoute"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Captura de ecrã de exemplo de Essentials do ExpressRoute"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






