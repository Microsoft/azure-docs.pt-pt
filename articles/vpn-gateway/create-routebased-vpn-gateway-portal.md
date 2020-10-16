---
title: 'Criar um gateway VPN baseado em rotas: portal'
titleSuffix: Azure VPN Gateway
description: Utilize o portal Azure para criar rapidamente um gateway Azure VPN baseado em rotas, para uma ligação VPN à sua rede no local ou para ligar redes virtuais.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 104d911164d0194efba41f1405c17fc240e8906d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393773"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Criar um gateway VPN baseado em rotas utilizando o portal Azure

Este artigo ajuda-o a criar rapidamente uma porta de entrada Azure VPN baseada em rotas utilizando o portal Azure.  Um gateway VPN é usado ao criar uma ligação VPN à sua rede no local. Também pode utilizar uma porta de entrada VPN para ligar VNets. 

Os passos deste artigo criarão um VNet, uma sub-rede, uma sub-rede de gateway, e um gateway VPN baseado em rotas (gateway de rede virtual). Uma vez concluída a criação do gateway, pode então criar ligações. Estes passos requerem uma assinatura Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Criar uma rede virtual

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Configure e crie o portal

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>O Gateway Basic SKU não suporta a autenticação IKEv2 ou RADIUS. Se planeia ter clientes Mac ligados à sua rede virtual, não utilize o SKU Básico.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Ver o portal VPN

1. Após a criação do gateway, navegue para vNet1 no portal. O gateway VPN aparece na página 'Vista Geral' como um dispositivo ligado.

   ![Dispositivos ligados](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Dispositivos ligados")

2. Na lista de dispositivos, clique em **VNet1GW** para ver mais informações.

   ![Ver gateway VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Ver gateway VPN")

## <a name="next-steps"></a>Passos seguintes

Uma vez que o gateway tenha terminado de criar, pode criar uma ligação entre a sua rede virtual e outro VNet. Ou criar uma ligação entre a sua rede virtual e um local no local.

> [!div class="nextstepaction"]
> [Criar uma ligação site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Criar uma ligação de ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Criar uma ligação a outro VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
