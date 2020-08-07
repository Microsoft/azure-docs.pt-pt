---
title: Configure um túnel VPN sempre ligado
titleSuffix: Azure VPN Gateway
description: Saiba como configurar Always On, que mantém uma ligação VPN baseada em gatilhos, tais como o sôm-in do utilizador, a mudança do estado da rede ou o ecrã do dispositivo ativo.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 24043576fca4910631ccddb3924303dd642c6842
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927067"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurar um túnel de dispositivo VPN AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurar o gateway

Configure a porta de entrada VPN para utilizar o IKEv2 e a autenticação baseada em certificados utilizando o artigo [de ligação VPN ponto-a-local.](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

## <a name="configure-the-device-tunnel"></a>Configure o túnel do dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover o perfil, executar o seguinte comando:

![Limpeza](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passos seguintes

Para resolver problemas, consulte [os problemas de ligação ponto-a-local do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
