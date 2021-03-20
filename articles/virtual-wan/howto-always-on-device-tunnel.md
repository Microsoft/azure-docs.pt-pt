---
title: Configurar um túnel VPN Always-On
titleSuffix: Azure Virtual WAN
description: Passos para configurar sempre no túnel do dispositivo VPN para O WAN Virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983692"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Configure um túnel de dispositivo Always On VPN para O WAN Virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Tem de criar uma configuração ponto-a-local e editar a atribuição do hub virtual. Consulte as seguintes secções para obter instruções:

* [Criar uma configuração P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Criar hub com gateway P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>Configure o túnel do dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover o perfil, executar o seguinte comando:

![A screenshot mostra uma janela PowerShell que executa o comando Remove-VpnConnection -Name MachineCertTest.](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)