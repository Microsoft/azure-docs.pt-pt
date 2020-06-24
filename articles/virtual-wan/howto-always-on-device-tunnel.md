---
title: Configure um túnel VPN sempre ligado
titleSuffix: Azure Virtual WAN
description: Passos para configurar sempre no túnel do dispositivo VPN para O WAN Virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e911bf6e3736c931ca5c1563ab42f52ecb5cf3c1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750574"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Configure um túnel de dispositivo Always On VPN para O WAN Virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Tem de criar uma configuração ponto-a-local e editar a atribuição do hub virtual. Consulte as seguintes secções para obter instruções:

* [Criar uma configuração P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Editar a atribuição do hub](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Configure o túnel do dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover o perfil, executar o seguinte comando:

![Limpeza](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)