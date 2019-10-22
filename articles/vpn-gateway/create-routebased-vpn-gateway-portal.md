---
title: 'Criar um gateway de VPN baseado em rota: portal do Azure | Microsoft Docs'
description: Criar um gateway de VPN baseado em rota usando o portal do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 3ab662a4f06b2d73ab0dab52f562398fee23686c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71266524"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Criar um gateway de VPN baseado em rota usando o portal do Azure

Este artigo ajuda você a criar rapidamente um gateway de VPN do Azure baseado em rota usando o portal do Azure.  Um gateway de VPN é usado ao criar uma conexão VPN com sua rede local. Você também pode usar um gateway de VPN para conectar o VNets. 

As etapas neste artigo criarão uma VNet, uma sub-rede, uma sub-rede de gateway e um gateway de VPN baseado em rota (gateway de rede virtual). Depois que a criação do gateway for concluída, você poderá criar conexões. Essas etapas exigem uma assinatura do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="vnet"></a>Criar uma rede virtual

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwvalues"></a>Configurar e criar o gateway

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>O SKU do gateway básico não oferece suporte à autenticação IKEv2 ou RADIUS. Se você planeja ter clientes Mac conectados à sua rede virtual, não use a SKU básica.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="viewgw"></a>Exibir o gateway de VPN

1. Depois que o gateway for criado, navegue até VNet1 no Portal. O gateway de VPN aparece na página Visão geral como um dispositivo conectado.

   ![Dispositivos conectados](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Dispositivos conectados")

2. Na lista de dispositivos, clique em **VNet1GW** para exibir mais informações.

   ![Exibir gateway de VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Exibir gateway de VPN")

## <a name="next-steps"></a>Passos seguintes

Depois que o gateway terminar de criar, você poderá criar uma conexão entre sua rede virtual e outra VNet. Ou crie uma conexão entre sua rede virtual e um local.

> [!div class="nextstepaction"]
> [Criar uma conexão site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Criar uma conexão ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Criar uma conexão com outra VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
