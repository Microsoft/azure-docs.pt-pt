---
title: Configurar clientes OpenVPN para Azure Virtual WAN
description: Passos para configurar clientes OpenVPN para Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3e9674916ea9bb5e756a5e57ff18517f53ca7497
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427563"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Configure um cliente OpenVPN para a Azure Virtual WAN

Este artigo ajuda-o a configurar clientes **&reg; do Protocolo OpenVPN.** Também pode utilizar o Cliente Azure VPN para o Windows 10 para ligar através do protocolo OpenVPN 

## <a name="before-you-begin"></a>Antes de começar

Crie uma configuração VPN do utilizador (ponto a local). Certifique-se de que seleciona "OpenVPN" para o tipo de túnel. Para obter passos, consulte [Criar uma configuração P2S para Azure Virtual WAN](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a VPN do utilizador (ponto a local), consulte [as ligações VPN do utilizador](virtual-wan-point-to-site-portal.md).

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
