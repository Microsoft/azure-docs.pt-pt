---
title: 'Criar um gateway VPN baseado em rota: portal'
titleSuffix: Azure VPN Gateway
description: Criar um VPN Gateway baseado em rota usando o portal Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6156d689a29ee348f9b1974d1520eb7d186a8d8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331354"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Criar um gateway VPN baseado em rota usando o portal Azure

Este artigo ajuda-o a criar rapidamente um gateway Azure VPN baseado em rotas utilizando o portal Azure.  Um gateway VPN é usado ao criar uma ligação VPN à sua rede no local. Também pode utilizar um gateway VPN para ligar VNets. 

Os passos neste artigo criarão um VNet, uma subnet, uma subnet gateway e um gateway VPN baseado em rota (gateway de rede virtual). Uma vez concluída a criação do portal, pode criar ligações. Estes passos requerem uma subscrição Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Criar uma rede virtual

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Configure e crie o portal

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>O Gateway Básico SKU não suporta a autenticação IKEv2 ou RADIUS. Se planeia que os clientes Mac se conectem à sua rede virtual, não utilize o SKU Básico.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Ver o gateway VPN

1. Depois de criado o portal, navegue para VNet1. O gateway VPN aparece na página 'Overview' como um dispositivo conectado.

   ![Dispositivos ligados](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Dispositivos ligados")

2. Na lista de dispositivos, clique em **VNet1GW** para ver mais informações.

   ![Ver gateway VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Ver gateway VPN")

## <a name="next-steps"></a>Passos seguintes

Uma vez que o gateway termine de criar, pode criar uma ligação entre a sua rede virtual e outro VNet. Ou criar uma ligação entre a sua rede virtual e um local no local.

> [!div class="nextstepaction"]
> [Criar uma ligação site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Criar uma ligação de ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Criar uma ligação a outro VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
