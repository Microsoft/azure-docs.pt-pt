---
title: 'Azure ExpressRoute: Adicionar suporte IPv6 utilizando o portal Azure'
description: Saiba como adicionar suporte IPv6 para ligar a implementações do Azure utilizando o portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: ef6ea9017a9aaa98e153df0d67f0b54fe5a2b64d
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124164"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Adicione suporte IPv6 para espreitar privado usando o portal Azure (Preview)

Este artigo descreve como adicionar suporte IPv6 para ligar via ExpressRoute aos seus recursos em Azure usando o portal Azure. 

> [!Note]
> Esta funcionalidade está atualmente disponível para pré-visualização nas [regiões do Azure com Zonas de Disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones) O seu circuito ExpressRoute pode, portanto, ser criado utilizando qualquer localização de observação, mas as implementações baseadas no IPv6 a que se conecta devem estar numa região com Zonas de Disponibilidade.

## <a name="register-for-public-preview"></a>Registro-se para visualização pública
Antes de adicionar suporte IPv6, deve primeiro inscrever a sua subscrição. Para se inscrever, por favor faça o seguinte através da Azure PowerShell:
1.  Inscreva-se no Azure e selecione a subscrição. Tem de o fazer pela subscrição que contém o seu circuito ExpressRoute, bem como pela subscrição que contém as suas implementações Azure (se forem diferentes).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registe a sua subscrição para Visualização Pública utilizando o seguinte comando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

O seu pedido será então aprovado pela equipa ExpressRoute dentro de 2-3 dias úteis.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

A partir de um navegador, vá ao [portal Azure](https://portal.azure.com)e, em seguida, inscreva-se na sua conta Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Adicione IPv6 Private Peering ao seu circuito ExpressRoute

1. [Crie um circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) ou navegue para o circuito existente que pretende alterar.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Navegue para o circuito":::

2. Selecione a configuração de espreitamento **privado Azure.**

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Navegue para o espreitar":::

3. Adicione um Peering Privado IPv6 à sua configuração de peering privado IPv4 existente selecionando "Ambos" para **sub-redes**, ou apenas ative o IPv6 Private Peering no seu novo circuito selecionando "IPv6". Forneça um par de sub-redes IPv6 de /126 que possui para o seu link primário e ligações secundárias. A partir de cada uma destas sub-redes, irá atribuir o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router. **Guarde** a sua configuração de espreitar uma vez especificado todos os parâmetros.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Adicionar peering privado IPv6":::

4. Depois de a configuração ter sido aceite com sucesso, deverá ver algo semelhante ao exemplo seguinte.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Ver o iPv6 peering privado":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Atualize a sua ligação a uma rede virtual existente

Siga os passos abaixo se tiver um ambiente existente de recursos Azure numa região com Zonas de Disponibilidade com as que gostaria de utilizar o seu IPv6 Private Peering com.

1. Navegue para a rede virtual a que o seu circuito ExpressRoute está ligado.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Navegue até à vnet":::

2. Navegue no separador **espaço address** e adicione um espaço de endereço IPv6 à sua rede virtual. **Guarde** o espaço do seu endereço.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Adicionar espaço de endereço IPv6":::

3. Navegue no separador **Subnetas** e selecione o **GatewaySubnet**. Verifique **o espaço de endereço IPv6** e forneça um espaço de endereço IPv6 para a sua sub-rede. A sub-rede IPv6 do gateway deve ser /64 ou maior. **Guarde** a sua configuração depois de especificar todos os parâmetros.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Adicione espaço de endereço IPv6 à sub-rede":::

4. Se tiver uma porta de entrada redundante de zona existente, navegue até ao seu gateway ExpressRoute e refresque o recurso para permitir a conectividade IPv6. Caso contrário, [crie o gateway de rede virtual](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) utilizando um SKU redundante de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se pretender utilizar o FastPath, utilize o ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Refresque o portal":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Criar uma ligação a uma nova rede virtual

Siga os passos abaixo se planeia ligar-se a um novo conjunto de recursos Azure numa região com Zonas de Disponibilidade utilizando o seu IPv6 Private Peering.

1. Crie uma rede virtual de dupla pilha com espaço de endereço IPv4 e IPv6. Para obter mais informações, consulte [Criar uma rede virtual.](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network)

2. [Crie a sub-rede de gateway de dupla pilha](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-gateway-subnet).

3. [Crie o gateway de rede virtual](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-virtual-network-gateway) utilizando um SKU redundante de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se pretender utilizar o FastPath, utilize o ErGw3AZ (note que este só está disponível para circuitos que utilizem o ExpressRoute Direct).

4. [Ligue a sua rede virtual ao circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

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
