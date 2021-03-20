---
title: Configurar clientes OpenVPN para Azure Virtual WAN
description: Passos para configurar clientes OpenVPN para Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491006"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Configure um cliente OpenVPN para a Azure Virtual WAN

Este artigo ajuda-o a configurar clientes **&reg; do Protocolo OpenVPN.** Também pode utilizar o Cliente Azure VPN para o Windows 10 para ligar através do protocolo OpenVPN. Mais instruções podem ser encontradas [aqui](openvpn-azure-ad-client.md)

## <a name="before-you-begin"></a>Antes de começar

Crie uma configuração VPN do utilizador (ponto a local). Certifique-se de que seleciona "OpenVPN" para o tipo de túnel. Para obter passos, consulte [Criar uma configuração P2S para Azure Virtual WAN](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a VPN do utilizador (ponto a local), consulte [as ligações VPN do utilizador](virtual-wan-point-to-site-portal.md).

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
