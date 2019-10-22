---
title: 'Verificar conectividade – guia de solução de problemas do ExpressRoute: Azure | Microsoft Docs'
description: Esta página fornece instruções sobre como solucionar problemas e validar a conectividade de ponta a ponta de um circuito do ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 026900e3dcbf7c20750bb8e17e44ba64897c9a30
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71123447"
---
# <a name="verifying-expressroute-connectivity"></a>Verificar a conectividade do ExpressRoute
Este artigo ajuda você a verificar e solucionar problemas de conectividade do ExpressRoute. O ExpressRoute, que estende uma rede local para o Microsoft Cloud por uma conexão privada que é facilitada por um provedor de conectividade, envolve as três seguintes zonas de rede distintas:

-   Rede do cliente
-   Rede do provedor
-   Datacenter da Microsoft

A finalidade deste documento é ajudar o usuário a identificar onde (ou mesmo se) um problema de conectividade existe e dentro de qual zona, por isso, para buscar ajuda da equipe apropriada para resolver o problema. Se o suporte da Microsoft for necessário para resolver um problema, abra um tíquete de suporte com [suporte da Microsoft][Support].

> [!IMPORTANT]
> Este documento destina-se a ajudar a diagnosticar e corrigir problemas simples. Não pretende ser uma substituição para o suporte da Microsoft. Abra um tíquete de suporte com [suporte da Microsoft][Support] se não for possível resolver o problema usando as diretrizes fornecidas.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral
O diagrama a seguir mostra a conectividade lógica de uma rede de cliente para a rede da Microsoft usando o ExpressRoute.
[![1]][1]

No diagrama anterior, os números indicam os principais pontos de rede. Os pontos de rede são referenciados com frequência neste artigo pelo número associado.

Dependendo do modelo de conectividade do ExpressRoute (colocalização de Cloud Exchange, conexão Ethernet ponto a ponto ou qualquer para qualquer (IPVPN)) os pontos de rede 3 e 4 podem ser comutadores (dispositivos de camada 2). Os principais pontos de rede ilustrados são os seguintes:

1.  Dispositivo de computação do cliente (por exemplo, um servidor ou PC)
2.  CEs: roteadores de borda do cliente 
3.  PEs (voltada para CE): provedores de borda do provedor/comutadores que estão enfrentando roteadores de borda do cliente. Referido como PE-CEs neste documento.
4.  PEs (MSEE face): roteadores/comutadores de borda do provedor voltados para o MSEEs. Referido como PE-MSEEs neste documento.
5.  MSEEs: roteadores do Microsoft Enterprise Edge (MSEE) ExpressRoute
6.  Gateway de rede virtual (VNet)
7.  Dispositivo de computação na VNet do Azure

Se os modelos de conectividade de conexão Ethernet do Cloud Exchange ou ponto a ponto forem usados, o roteador de borda do cliente (2) estabelecerá o emparelhamento via protocolo BGP com MSEEs (5). Os pontos de rede 3 e 4 ainda existirão, mas seriam um tanto transparentes como dispositivos de camada 2.

Se o modelo de conectividade de qualquer para qualquer (IPVPN) for usado, o PEs (voltada para o MSEE) (4) estabelecerá o emparelhamento via protocolo BGP com MSEEs (5). As rotas seriam propagadas de volta para a rede do cliente por meio da rede do provedor de serviços do IPVPN.

> [!NOTE]
>Para alta disponibilidade do ExpressRoute, a Microsoft requer um par redundante de sessões BGP entre MSEEs (5) e PE-MSEEs (4). Um par redundante de caminhos de rede também é incentivado entre a rede do cliente e o PE-CEs. No entanto, em um modelo de conexão de qualquer para qualquer (IPVPN), um único dispositivo CE (2) pode estar conectado a um ou mais PEs (3).
>
>

Para validar um circuito do ExpressRoute, as etapas a seguir são cobertas (com o ponto de rede indicado pelo número associado):
1. [Validar o provisionamento e o estado do circuito (5)](#validate-circuit-provisioning-and-state)
2. [Validar pelo menos um emparelhamento de ExpressRoute está configurado (5)](#validate-peering-configuration)
3. [Validar o ARP entre a Microsoft e o provedor de serviços (link entre 4 e 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Valide o BGP e as rotas no MSEE (BGP entre 4 e 5 e 5 a 6 se uma VNet estiver conectada)](#validate-bgp-and-routes-on-the-msee)
5. [Verificar as estatísticas de tráfego (tráfego que passa por 5)](#check-the-traffic-statistics)

Mais validações e verificações serão adicionadas no futuro, verifique-as mensalmente!

## <a name="validate-circuit-provisioning-and-state"></a>Validar o estado e o provisionamento do circuito
Independentemente do modelo de conectividade, um circuito de ExpressRoute deve ser criado e, portanto, uma chave de serviço gerada para provisionamento de circuito. O provisionamento de um circuito do ExpressRoute estabelece conexões de camada 2 redundantes entre PE-MSEEs (4) e MSEEs (5). Para obter mais informações sobre como criar, modificar, provisionar e verificar um circuito do ExpressRoute, consulte o artigo [criar e modificar um circuito do expressroute][CreateCircuit].

>[!TIP]
>Uma chave de serviço identifica exclusivamente um circuito do ExpressRoute. Essa chave é necessária para a maioria dos comandos do PowerShell mencionados neste documento. Além disso, caso você precise de assistência da Microsoft ou de um parceiro de ExpressRoute para solucionar um problema de ExpressRoute, forneça a chave de serviço para identificar prontamente o circuito.
>
>

### <a name="verification-via-the-azure-portal"></a>Verificação por meio do portal do Azure
No portal do Azure, o status de um circuito do ExpressRoute pode ser verificado selecionando ![2][2] no menu da barra do lado esquerdo e selecionando o circuito do ExpressRoute. Selecionar um circuito do ExpressRoute listado em "todos os recursos" abre a folha do circuito do ExpressRoute. Na seção ![3][3] da folha, o ExpressRoute Essentials é listado conforme mostrado na seguinte captura de tela:

![4][4]    

No ExpressRoute Essentials, o *status do circuito* indica o status do circuito no lado da Microsoft. *Status do provedor* indica se o circuito foi *provisionado/não provisionado* no lado do provedor de serviço. 

Para que um circuito do ExpressRoute seja operacional, o *status do circuito* deve ser *habilitado* e o *status do provedor* deve ser *provisionado*.

> [!NOTE]
> Se o *status do circuito* não estiver habilitado, entre em contato com [suporte da Microsoft][Support]. Se o *status do provedor* não for provisionado, entre em contato com seu provedor de serviços.
>
>

### <a name="verification-via-powershell"></a>Verificação por meio do PowerShell
Para listar todos os circuitos do ExpressRoute em um grupo de recursos, use o seguinte comando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Você pode obter o nome do grupo de recursos por meio do Azure. Consulte a subseção anterior deste documento e observe que o nome do grupo de recursos está listado na captura de tela de exemplo.
>
>

Para selecionar um circuito de ExpressRoute específico em um grupo de recursos, use o seguinte comando:

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

Para confirmar se um circuito do ExpressRoute está operacional, preste atenção especial aos seguintes campos:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Se o *CircuitProvisioningState* não estiver habilitado, entre em contato com [suporte da Microsoft][Support]. Se o *ServiceProviderProvisioningState* não for provisionado, entre em contato com seu provedor de serviços.
>
>

### <a name="verification-via-powershell-classic"></a>Verificação por meio do PowerShell (clássico)
Para listar todos os circuitos do ExpressRoute em uma assinatura, use o seguinte comando:

    Get-AzureDedicatedCircuit

Para selecionar um circuito de ExpressRoute específico, use o seguinte comando:

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

Para confirmar se um circuito do ExpressRoute está operacional, preste atenção especial aos seguintes campos: ServiceProviderProvisioningState: status provisionado: habilitado

> [!NOTE]
> Se o *status* não estiver habilitado, entre em contato com [suporte da Microsoft][Support]. Se o *ServiceProviderProvisioningState* não for provisionado, entre em contato com seu provedor de serviços.
>
>

## <a name="validate-peering-configuration"></a>Validar a configuração de emparelhamento
Depois que o provedor de serviços tiver concluído o provisionamento do circuito do ExpressRoute, uma configuração de roteamento poderá ser criada no circuito do ExpressRoute entre MSEE-PRs (4) e MSEEs (5). Cada circuito do ExpressRoute pode ter um, dois ou três contextos de roteamento habilitados: emparelhamento privado do Azure (tráfego para redes virtuais privadas no Azure), emparelhamento público do Azure (tráfego para endereços IP públicos no Azure) e emparelhamento da Microsoft (tráfego para o Office 365). Para obter mais informações sobre como criar e modificar a configuração de roteamento, consulte o artigo [criar e modificar o roteamento de um circuito do ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verificação por meio do portal do Azure

> [!NOTE]
> Se a camada 3 for fornecida pelo provedor de serviços e os emparelhamentos estiverem em branco no portal, atualize a configuração do circuito usando o botão Atualizar no Portal. Esta operação aplicará a configuração de roteamento correta em seu circuito. 
>
>

No portal do Azure, o status de um circuito do ExpressRoute pode ser verificado selecionando ![2][2] no menu da barra do lado esquerdo e, em seguida, selecionando o circuito do ExpressRoute. Selecionar um circuito do ExpressRoute listado em "todos os recursos" abriria a folha do circuito do ExpressRoute. Na seção ![3][3] da folha, o ExpressRoute Essentials seria listado conforme mostrado na seguinte captura de tela:

![5][5]

No exemplo anterior, conforme observado, o contexto de roteamento de emparelhamento privado do Azure está habilitado, enquanto os contextos de roteamento de emparelhamento público e da Microsoft do Azure não estão habilitados. Um contexto de emparelhamento habilitado com êxito também teria as sub-redes primária e secundária ponto a ponto (obrigatórias para BGP) listadas. As sub-redes/30 são usadas para o endereço IP da interface do MSEEs e do PE-MSEEs. 

> [!NOTE]
> Se um emparelhamento não estiver habilitado, verifique se as sub-redes primárias e secundárias atribuídas correspondem à configuração no PE-MSEEs. Caso contrário, para alterar a configuração em roteadores MSEE, consulte [criar e modificar o roteamento para um circuito de ExpressRoute][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Verificação por meio do PowerShell
Para obter os detalhes de configuração de emparelhamento privado do Azure, use os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Uma resposta de exemplo, para um emparelhamento privado configurado com êxito, é:

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

 Um contexto de emparelhamento habilitado com êxito teria os prefixos de endereço primário e secundário listados. As sub-redes/30 são usadas para o endereço IP da interface do MSEEs e do PE-MSEEs.

Para obter os detalhes de configuração de emparelhamento público do Azure, use os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Para obter os detalhes de configuração de emparelhamento da Microsoft, use os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Se um emparelhamento não estiver configurado, haveria uma mensagem de erro. Uma resposta de exemplo, quando o emparelhamento mencionado (emparelhamento público do Azure neste exemplo) não está configurado no circuito:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Se um emparelhamento não estiver habilitado, verifique se as sub-redes primárias e secundárias atribuídas correspondem à configuração no PE-MSEE vinculado. Verifique também se o *vlanid*, o *AzureASN*e o *PeerASN* corretos são usados em MSEEs e se esses valores são mapeados para aqueles usados no PE-MSEE vinculado. Se o hash MD5 for escolhido, a chave compartilhada deverá ser a mesma no par MSEE e PE-MSEE. Para alterar a configuração nos roteadores MSEE, consulte [criar e modificar o roteamento para um circuito do ExpressRoute][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Verificação por meio do PowerShell (clássico)
Para obter os detalhes de configuração de emparelhamento privado do Azure, use o seguinte comando:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Uma resposta de exemplo para um emparelhamento privado configurado com êxito é:

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

Um contexto de emparelhamento habilitado com êxito teria as sub-redes emparelhadas primárias e secundárias listadas. As sub-redes/30 são usadas para o endereço IP da interface do MSEEs e do PE-MSEEs.

Para obter os detalhes de configuração de emparelhamento público do Azure, use os seguintes comandos:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Para obter os detalhes de configuração de emparelhamento da Microsoft, use os seguintes comandos:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> Se os emparelhamentos de camada 3 foram definidos pelo provedor de serviços, definir os emparelhamentos de ExpressRoute por meio do portal ou do PowerShell substituirá as configurações do provedor de serviços. Redefinir as configurações de emparelhamento do lado do provedor requer o suporte do provedor de serviços. Modifique somente os emparelhamentos do ExpressRoute se tiver certeza de que o provedor de serviços está fornecendo somente serviços de camada 2!
>
>

> [!NOTE]
> Se um emparelhamento não estiver habilitado, verifique se as sub-redes emparelhadas primárias e secundárias atribuídas correspondem à configuração no PE-MSEE vinculado. Verifique também se o *vlanid*, o *AzureAsn*e o *PeerAsn* corretos são usados em MSEEs e se esses valores são mapeados para aqueles usados no PE-MSEE vinculado. Para alterar a configuração nos roteadores MSEE, consulte [criar e modificar o roteamento para um circuito do ExpressRoute][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Validar o ARP entre a Microsoft e o provedor de serviços
Esta seção usa comandos do PowerShell (clássicos). Se você estiver usando os comandos do PowerShell Azure Resource Manager, verifique se você tem acesso de administrador/coadministrador à assinatura. Para solucionar problemas usando comandos Azure Resource Manager, consulte o documento [obtendo tabelas ARP no modelo de implantação do Gerenciador de recursos][ARP] .

> [!NOTE]
>Para obter o ARP, os comandos portal do Azure e Azure Resource Manager PowerShell podem ser usados. Se forem encontrados erros com os comandos do Azure Resource Manager PowerShell, os comandos clássicos do PowerShell devem funcionar como comandos clássicos do PowerShell também funcionam com Azure Resource Manager circuitos do ExpressRoute.
>
>

Para obter a tabela ARP do roteador MSEE primário para o emparelhamento privado, use o seguinte comando:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Um exemplo de resposta para o comando, no cenário bem-sucedido:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Da mesma forma, você pode verificar a tabela ARP do MSEE no caminho*secundário* / *primário* ,*para /* *privadas* / emparelhamentos da*Microsoft* .

O exemplo a seguir mostra a resposta do comando para um emparelhamento que não existe.

    ARP Info:
       
> [!NOTE]
> Se a tabela ARP não tiver endereços IP das interfaces mapeadas para endereços MAC, examine as seguintes informações:
>1. Se o primeiro endereço IP da sub-rede/30 atribuído para o link entre MSEE-PR e MSEE for usado na interface de MSEE-PR. O Azure sempre usa o segundo endereço IP para MSEEs.
>2. Verifique se as marcas de VLAN Customer (C-tag) e Service (S-tag) correspondem ambas no par MSEE-PR e MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Validar BGP e rotas no MSEE
Esta seção usa comandos do PowerShell (clássicos). Se você estiver usando os comandos do PowerShell Azure Resource Manager, verifique se você tem acesso de administrador/coadministrador à assinatura.

> [!NOTE]
>Para obter informações de BGP, os comandos portal do Azure e Azure Resource Manager PowerShell podem ser usados. Se forem encontrados erros com os comandos do Azure Resource Manager PowerShell, os comandos clássicos do PowerShell devem funcionar como comandos clássicos do PowerShell também funcionam com Azure Resource Manager circuitos do ExpressRoute.
>
>

Para obter o resumo da tabela de roteamento (vizinho BGP) para um determinado contexto de roteamento, use o seguinte comando:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Uma resposta de exemplo é:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Conforme mostrado no exemplo anterior, o comando é útil para determinar por quanto tempo o contexto de roteamento foi estabelecido. Ele também indica o número de prefixos de rota anunciados pelo roteador de emparelhamento.

> [!NOTE]
> Se o estado estiver ativo ou ocioso, verifique se as sub-redes emparelhadas primárias e secundárias atribuídas correspondem à configuração no PE-MSEE vinculado. Verifique também se o *vlanid*, o *AzureAsn*e o *PeerAsn* corretos são usados em MSEEs e se esses valores são mapeados para aqueles usados no PE-MSEE vinculado. Se o hash MD5 for escolhido, a chave compartilhada deverá ser a mesma no par MSEE e PE-MSEE. Para alterar a configuração nos roteadores MSEE, consulte [criar e modificar o roteamento para um circuito do ExpressRoute][CreatePeering].
>
>

> [!NOTE]
> Se determinados destinos não estiverem acessíveis em um emparelhamento específico, verifique a tabela de rotas do MSEEs que pertence ao contexto de emparelhamento específico. Se um prefixo correspondente (pode ser IP de NATe) estiver presente na tabela de roteamento, verifique se há firewalls/NSG/ACLs no caminho e se eles permitem o tráfego.
>
>

Para obter a tabela de roteamento completo de MSEE no caminho *primário* para o contexto de roteamento *particular* específico, use o seguinte comando:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Um resultado de exemplo bem-sucedido para o comando é:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Da mesma forma, você pode verificar a tabela de roteamento do MSEE no caminho*secundário* de / *primário* , para *privado* /*público* / contexto de emparelhamento da*Microsoft* .

O exemplo a seguir mostra a resposta do comando para um emparelhamento que não existe:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Verificar as estatísticas de tráfego
Para obter as estatísticas de tráfego de caminho primário e secundário combinadas--bytes de entrada e saída--de um contexto de emparelhamento, use o seguinte comando:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Uma saída de exemplo do comando é:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Um exemplo de saída do comando para um emparelhamento não existente é:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações ou ajuda, confira os links a seguir:

- [Suporte da Microsoft][Support]
- [Criar e modificar um circuito ExpressRoute][CreateCircuit]
- [Criar e modificar o roteamento de um circuito do ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "conectividade lógica de rota expressa"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ícone todos os recursos"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ícone de visão geral"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Captura de tela de exemplo do ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Captura de tela de exemplo do ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






