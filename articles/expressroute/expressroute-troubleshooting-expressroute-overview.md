---
title: 'Azure ExpressRoute: Verificar conectividade - Guia de resolução de problemas'
description: Esta página fornece instruções sobre a resolução de problemas e validação da conectividade final até ao fim de um circuito ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: af4ef156cccded6afe2db09628446a6ffe1ad53a
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92204644"
---
# <a name="verifying-expressroute-connectivity"></a>Verificar a conectividade do ExpressRoute
Este artigo ajuda-o a verificar e a resolver problemas a conectividade ExpressRoute. O ExpressRoute estende uma rede no local para a nuvem da Microsoft sobre uma ligação privada que é geralmente facilitada por um fornecedor de conectividade. A conectividade ExpressRoute envolve tradicionalmente três zonas de rede distintas, da seguinte forma:

-   Rede de Cliente
-   Rede de Fornecedores
-   Centro de Dados da Microsoft

> [!NOTE]
> No modelo de conectividade direta ExpressRoute (oferecido em largura de banda de 10/100 Gbps), os clientes podem ligar-se diretamente à porta dos routers microsoft Enterprise Edge (MSEE). Portanto, no modelo de conectividade direta, existem apenas zonas de rede de clientes e Microsoft.
>


O objetivo deste documento é ajudar o utilizador a identificar se e onde existe um problema de conectividade. Assim, para ajudar a procurar apoio da equipa apropriada para resolver um problema. Se o suporte da Microsoft for necessário para resolver um problema, abra um bilhete de suporte com [o Microsoft Support][Support].

> [!IMPORTANT]
> Este documento destina-se a ajudar a diagnosticar e corrigir questões simples. Não se pretende que seja um substituto para o suporte da Microsoft. Abra um bilhete de suporte com [o Microsoft Support][Support] se não conseguir resolver o problema utilizando as orientações fornecidas.
>
>

## <a name="overview"></a>Descrição geral
O diagrama seguinte mostra a conectividade lógica de uma rede de clientes com a rede Microsoft utilizando o ExpressRoute.
[![1]][1]

No diagrama anterior, os números indicam pontos-chave de rede. Estes pontos de rede são referenciados neste artigo por vezes pelo seu número associado. Dependendo do modelo de conectividade ExpressRoute-- Cloud Exchange Co-localização, Ligação Ethernet ponto-a-ponto ou qualquer um (IPVPN)-- os pontos de rede 3 e 4 podem ser comutadores (dispositivos da Camada 2) ou routers (dispositivos da Camada 3). No modelo de conectividade direta, não existem pontos de rede 3 e 4; em vez disso, os CEs (2) estão diretamente ligados aos MSEEs através de fibra escura. Os principais pontos de rede ilustrados são os seguintes:

1.  Dispositivo de computação do cliente (por exemplo, um servidor ou PC)
2.  CEs: Routers de borda do cliente 
3.  PEs (virado CE): Routers/comutadores de borda do fornecedor que estão virados para os routers de borda do cliente. Referido como PE-CEs neste documento.
4.  PEs (virado para o MSEE): Routers/comutadores de borda do fornecedor que estão virados para os MSEEs. Referido como PE-MSEEs neste documento.
5.  MSEEs: Microsoft Enterprise Edge (MSEE) ExpressRoute routers
6.  Gateway de Rede Virtual (VNet)
7.  Dispositivo computacional no Azure VNet

Se forem utilizados os modelos de Co-localização cloud Exchange, Point-to-Point Ethernet ou conectividade direta, os CEs (2) estabelecem o par de BGP com MSEEs (5). 

Se for utilizado o modelo de conectividade Any-to-Any (IPVPN), PE-MSEEs (4) estabelecer o par de BGP com OS MSEEs (5). PE-MSEEs propagar as rotas recebidas da Microsoft de volta à rede de clientes através da rede de prestadores de serviços IPVPN.

> [!NOTE]
>Para uma elevada disponibilidade, a Microsoft estabelece uma conectividade paralela totalmente redundante entre pares MSEEs (5) e PE-MSEEs (4). Um caminho de rede paralelo totalmente redundante também é encorajado entre a rede de clientes e PE-CEs par. Para obter mais informações sobre a elevada disponibilidade, consulte o artigo [Projetando para alta disponibilidade com ExpressRoute][HA]
>
>

Seguem-se os passos lógicos no circuito ExpressRoute:

* [Verificar o provisionamento e o estado do circuito](#verify-circuit-provisioning-and-state)
  
* [Validar configuração de peering](#validate-peering-configuration)
  
* [Validar ARP](#validate-arp)
  
* [Validar bGP e rotas no MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Confirme o fluxo de tráfego](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Verificar o provisionamento e o estado do circuito
A provisionamento de um circuito ExpressRoute estabelece uma ligação redundante da Camada 2 entre os EES/PE-MSEEs (2)/4) e os MSEEs (5). Para obter mais informações sobre como criar, modificar, providenciar e verificar um circuito ExpressRoute, consulte o artigo [Criar e modificar um circuito ExpressRoute][CreateCircuit].

>[!TIP]
>Uma chave de serviço identifica exclusivamente um circuito ExpressRoute. Caso necessite de assistência da Microsoft ou de um parceiro ExpressRoute para resolver problemas num problema expressRoute, forneça a chave de serviço para identificar facilmente o circuito.
>
>

### <a name="verification-via-the-azure-portal"></a>Verificação através do portal Azure
No portal Azure, abra a lâmina do circuito ExpressRoute. Na secção ![3][3] da lâmina, os elementos essenciais ExpressRoute estão listados como mostrado na seguinte imagem:

![4][4]    

No ExpressRoute Essentials, o *estado do circuito* indica o estado do circuito no lado da Microsoft. *O estado do fornecedor* indica se o circuito foi *provisionado/não a provisionado* do lado do prestador de serviços. 

Para que um circuito ExpressRoute esteja operacional, o estado do *circuito* deve ser *ativado* e o *estado do Fornecedor* deve ser *previsto*.

> [!NOTE]
> Depois de configurar um circuito ExpressRoute, se o estado do *Circuito* for atingido em estado não ativado, contacte o [Microsoft Support][Support]. Por outro lado, se o *estado do Fornecedor* for atingido em estado não previsto, contacte o seu prestador de serviços.
>
>

### <a name="verification-via-powershell"></a>Verificação via PowerShell
Para listar todos os circuitos ExpressRoute num Grupo de Recursos, utilize o seguinte comando:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"
```

>[!TIP]
>Se estiver à procura do nome de um grupo de recursos, pode obtê-lo listando todos os grupos de recursos da sua subscrição, utilizando o comando *Get-AzResourceGroup*
>


Para selecionar um determinado circuito ExpressRoute num Grupo de Recursos, utilize o seguinte comando:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
```

Uma resposta da amostra é:

```output
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
```

Para confirmar se um circuito ExpressRoute está operacional, preste especial atenção aos seguintes campos:

```output
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
```

> [!NOTE]
> Depois de configurar um circuito ExpressRoute, se o estado do *Circuito* for atingido em estado não ativado, contacte o [Microsoft Support][Support]. Por outro lado, se o *estado do Fornecedor* for atingido em estado não previsto, contacte o seu prestador de serviços.
>
>

## <a name="validate-peering-configuration"></a>Validar configuração de peering
Após o prestador de serviços ter concluído o fornecimento do circuito ExpressRoute, podem ser criadas várias configurações de encaminhamento baseadas em eBGP no circuito ExpressRoute entre CEs/MSEE-PEs (2)/4) e MSEEs (5). Cada circuito ExpressRoute pode ter: Azure private peering (tráfego para redes virtuais privadas em Azure), e/ou Microsoft espreitando (tráfego para pontos finais públicos de PaaS e SaaS). Para obter mais informações sobre como criar e modificar a configuração do encaminhamento, consulte o artigo [Criar e modificar o encaminhamento para um circuito ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verificação através do portal Azure

> [!NOTE]
> No modelo de conectividade IPVPN, os prestadores de serviços lidam com a responsabilidade de configurar os pares (serviços de camada 3). Neste modelo, depois de o prestador de serviços ter configurado um espreitamento e se o espreitamento estiver em branco no portal, tente refrescar a configuração do circuito utilizando o botão de atualização no portal. Esta operação retirará a configuração de encaminhamento atual do seu circuito. 
>

No portal Azure, o estado de um circuito ExpressRoute pode ser verificado sob a lâmina do circuito ExpressRoute. Na secção ![3][3] da lâmina, os olhos expressRoute seriam listados como mostrado na seguinte imagem:

![5][5]

No exemplo anterior, como se nota, o espreitamento privado Azure é a provisionado, enquanto os seus pares públicos e microsoft não são a provisionados. Um contexto de observação bem-sucedido teria também as sub-redes primárias e secundárias de ponto a ponto listadas. As sub-redes /30 são utilizadas para o endereço IP de interface dos MSEEs e CEs/PE-MSEEs. Para os os seus pares que são a provisionados, a listagem também indica quem modificou pela última vez a configuração. 

> [!NOTE]
> Se ativar um estorte falhar, verifique se as subnetas primárias e secundárias atribuídas correspondem à configuração do CE/PE-MSEE ligado. Verifique também se os *VlanId*corretos, *AzureASN*e *PeerASN* são utilizados em MSEEs e se estes valores mapeiam para os utilizados no CE/PE-MSEE ligado. Se o hashing MD5 for escolhido, a chave partilhada deve ser a mesma no par MSEE e PE-MSEE/CE. A chave partilhada previamente configurada não seria apresentada por razões de segurança. Se necessitar de alterar qualquer uma destas configurações num router MSEE, consulte-se para [Criar e modificar o encaminhamento para um circuito ExpressRoute][CreatePeering].  
>

> [!NOTE]
> Numa sub-rede /30 atribuída à interface, a Microsoft escolherá o segundo endereço IP utilizável da sub-rede para a interface MSEE. Por conseguinte, certifique-se de que o primeiro endereço IP utilizável da sub-rede foi atribuído no CE/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Verificação via PowerShell
Para obter os detalhes de configuração de observação privado do Azure, utilize os seguintes comandos:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

Uma resposta de amostra, para um olhar privado configurado com sucesso, é:

```output
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
```

 Um contexto de observação bem-sucedido teria os prefixos de endereço primário e secundário listados. As sub-redes /30 são utilizadas para o endereço IP de interface dos MSEEs e CEs/PE-MSEEs.

Para obter os detalhes de configuração de observação do público Azure, utilize os seguintes comandos:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
```

Para obter os detalhes de configuração de esquisito da Microsoft, utilize os seguintes comandos:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

Se um olhar não estiver configurado, haverá uma mensagem de erro. Uma resposta da amostra, quando o espreitamento declarado (Azure Public espreitando neste exemplo) não está configurado dentro do circuito:

```azurepowershell
Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
At line:1 char:1
    + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
        + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand
```

> [!NOTE]
> Se ativar um estorte falhar, verifique se as subnetas primárias e secundárias atribuídas correspondem à configuração do CE/PE-MSEE ligado. Verifique também se os *VlanId*corretos, *AzureASN*e *PeerASN* são utilizados em MSEEs e se estes valores mapeiam para os utilizados no CE/PE-MSEE ligado. Se o hashing MD5 for escolhido, a chave partilhada deve ser a mesma no par MSEE e PE-MSEE/CE. A chave partilhada previamente configurada não seria apresentada por razões de segurança. Se necessitar de alterar qualquer uma destas configurações num router MSEE, consulte-se para [Criar e modificar o encaminhamento para um circuito ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> Numa sub-rede /30 atribuída à interface, a Microsoft escolherá o segundo endereço IP utilizável da sub-rede para a interface MSEE. Por conseguinte, certifique-se de que o primeiro endereço IP utilizável da sub-rede foi atribuído no CE/PE-MSEE.
>

## <a name="validate-arp"></a>Validar ARP

A tabela ARP fornece um mapeamento do endereço IP e endereço MAC para um olhar específico. A tabela ARP para um perspério do circuito ExpressRoute fornece as seguintes informações para cada interface (primária e secundária):
* Mapeamento do endereço IP da interface do router no local para o endereço MAC
* Mapeamento do endereço IP da interface do router ExpressRoute para o endereço MAC
* A idade das tabelas ARP de mapeamento pode ajudar a validar a configuração da camada 2 e resolver problemas básicos de conectividade da camada 2.


Consulte [as tabelas ARP no documento do modelo de implementação do Gestor de Recursos,][ARP] para ver a tabela ARP de um olho expressoRoute e como usar a informação para resolver problemas de conectividade da camada 2.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Validar bGP e rotas no MSEE

Para obter a tabela de encaminhamento do MSEE no caminho *primário* para o contexto de encaminhamento *privado,* utilize o seguinte comando:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****
```

Uma resposta de exemplo é:

```output
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
```

> [!NOTE]
> Se o estado de um eBGP que espreita entre um MSEE e um CE/PE-MSEE estiver em Ativo ou Idle, verifique se as sub-redes de pares primárias e secundárias atribuídas correspondem à configuração no CE/PE-MSEE ligado. Verifique também se os *VlanId*corretos, *AzureAsn*e *PeerAsn* são utilizados em MSEEs e se estes valores mapeiam para os utilizados no PE-MSEE/CE ligado. Se o hashing MD5 for escolhido, a chave partilhada deve ser a mesma no par MSEE e CE/PE-MSEE. Se necessitar de alterar qualquer uma destas configurações num router MSEE, consulte-se para [Criar e modificar o encaminhamento para um circuito ExpressRoute][CreatePeering].
>


> [!NOTE]
> Se determinados destinos não forem acessíveis ao longo de um espreitamento, verifique a tabela de rotas dos PME para o contexto de observação correspondente. Se um prefixo correspondente (pode ser NATed IP) estiver presente na tabela de encaminhamento, verifique se existem firewalls/NSG/ACLs no caminho que bloqueia o tráfego.
>


O exemplo a seguir mostra a resposta do comando para um espreguiva que não existe:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="confirm-the-traffic-flow"></a>Confirme o fluxo de tráfego
Para obter as estatísticas combinadas de tráfego de vias primárias e secundárias -- bytes dentro e fora -- de um contexto de espreitar, use o seguinte comando:

```azurepowershell
Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'
```

Uma amostra de saída do comando é:

```output
PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
-------------- --------------- ---------------- -----------------
     240780020       239863857        240565035         239628474
```

Uma amostra do comando para um espreitamento inexistente é:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="next-steps"></a>Passos Seguintes
Para mais informações ou ajuda, confira os seguintes links:

- [Suporte à Microsoft][Support]
- [Criar e modificar um circuito ExpressRoute][CreateCircuit]
- [Criar e modificar o encaminhamento de um circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Conectividade lógico da rota expressa"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Todo o ícone de recursos"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ícone de visão geral"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials screenshot amostra"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials screenshot amostra"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ARP]: ./expressroute-troubleshooting-arp-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[DR-Pvt]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md