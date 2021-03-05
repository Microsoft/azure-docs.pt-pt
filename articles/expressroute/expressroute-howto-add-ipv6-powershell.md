---
title: 'Azure ExpressRoute: Adicionar suporte IPv6 usando Azure PowerShell'
description: Saiba como adicionar suporte IPv6 para ligar a implementações do Azure utilizando a Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 253fce7d47d694c03d470fefdf81318a6bff77b3
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123059"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Adicione suporte IPv6 para espreitar privado usando Azure PowerShell (Preview)

Este artigo descreve como adicionar suporte IPv6 para ligar via ExpressRoute aos seus recursos em Azure usando Azure PowerShell.

> [!Note]
> Esta funcionalidade está atualmente disponível para pré-visualização nas [regiões do Azure com Zonas de Disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones) O seu circuito ExpressRoute pode, portanto, ser criado utilizando qualquer localização de observação, mas as implementações baseadas no IPv6 a que se conecta devem estar numa região com Zonas de Disponibilidade.

## <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>Registro-se para visualização pública
Antes de adicionar suporte IPv6, deve primeiro inscrever a sua subscrição. Para se inscrever, por favor faça o seguinte através da Azure PowerShell:
1.  Inscreva-se no Azure e selecione a subscrição. Tem de o fazer pela subscrição que contém o seu circuito ExpressRoute, bem como pela subscrição que contém as suas implementações Azure (se forem diferentes).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Solicitação para registar a sua subscrição para Visualização Pública utilizando o seguinte comando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

O seu pedido será então aprovado pela equipa ExpressRoute dentro de 2-3 dias úteis.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Adicione IPv6 Private Peering ao seu circuito ExpressRoute

1. [Crie um circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-arm) ou utilize um circuito existente. Recupere o circuito executando o comando **Get-AzExpressRouteCircuit:**

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Recupere a configuração de espreitamento privado para o circuito executando **Get-AzExpressCircuitPeeringConfig:**

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Adicione um IPv6 Private Peering à sua configuração de peering privado IPv4 existente. Forneça um par de sub-redes IPv6 de /126 que possui para o seu link primário e ligações secundárias. A partir de cada uma destas sub-redes, irá atribuir o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router.

    > [!Note]
    > O peer ASN e o VlanId devem coincidir com os da sua configuração de Peering Privado IPv4.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Depois de a configuração ter sido guardada com sucesso, volte a obter o circuito executando o comando **Get-AzExpressRouteCircuit.** A resposta deve ser semelhante ao seguinte exemplo:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
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
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Atualize a sua ligação a uma rede virtual existente

Siga os passos abaixo se tiver um ambiente existente de recursos Azure numa região com Zonas de Disponibilidade com as que gostaria de utilizar o seu IPv6 Private Peering com.

1. Recupere a rede virtual a que o seu circuito ExpressRoute está ligado.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Adicione um espaço de endereço IPv6 à sua rede virtual.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Adicione espaço de endereço IPv6 à sua sub-rede gateway. A sub-rede IPv6 do gateway deve ser /64 ou maior.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Se tiver uma porta de entrada redundante de zona existente, corra o seguinte para permitir a conectividade IPv6. Caso contrário, [crie o gateway de rede virtual](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager) utilizando um SKU redundante de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Criar uma ligação a uma nova rede virtual

Siga os passos abaixo se planeia ligar-se a um novo conjunto de recursos Azure numa região com Zonas de Disponibilidade utilizando o seu IPv6 Private Peering.

1. Crie uma rede virtual de dupla pilha com espaço de endereço IPv4 e IPv6. Para obter mais informações, consulte [Criar uma rede virtual.](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network)

2. [Crie a sub-rede de gateway de dupla pilha](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway).

3. [Crie o gateway de rede virtual](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway) utilizando um SKU redundante de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se pretender utilizar o FastPath, utilize o ErGw3AZ (note que este só está disponível para circuitos que utilizem o ExpressRoute Direct).

4. [Ligue a sua rede virtual ao circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm).

## <a name="limitations"></a>Limitações
Embora o suporte IPv6 esteja disponível para ligações a implementações em regiões com Zonas de Disponibilidade, não suporta os seguintes casos de utilização:

* Ligações a implantações em Azure através de um gateway não-AZ ExpressRoute SKU
* Ligações a implantações em regiões não-AZ
* Ligações Global Reach entre circuitos ExpressRoute
* Utilização do ExpressRoute com WAN virtual
* FastPath com circuitos diretos não ExpressRoute
* Coexistência com VPN Gateway

## <a name="next-steps"></a>Passos seguintes

Para resolver problemas com o ExpressRoute, consulte os seguintes artigos:

* [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Resolver problemas de desempenho da rede](expressroute-troubleshooting-network-performance.md)
