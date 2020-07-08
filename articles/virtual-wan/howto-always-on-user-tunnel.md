---
title: Configure um túnel de utilizador VPN sempre ligado
titleSuffix: Azure Virtual WAN
description: Este artigo descreve como configurar um túnel de utilizador Always On VPN para o seu WAN Virtual
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 03f67053a5a199c8c64efb05d2b6a65ad6707650
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564049"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Configure um túnel de utilizador Always On VPN para O WAN Virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Tem de criar uma configuração ponto-a-local e editar a atribuição do hub virtual. Consulte as seguintes secções para obter instruções:

* [Criar uma configuração P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Criar hub com gateway P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>Configure um túnel de utilizador

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, utilize os seguintes passos:

1. Execute o seguinte comando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desligue a ligação e limpe a caixa de verificação **do Connect automaticamente.**

   ![Limpeza](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
