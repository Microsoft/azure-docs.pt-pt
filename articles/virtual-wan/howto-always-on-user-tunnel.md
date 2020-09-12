---
title: Configure um túnel de utilizador VPN sempre ligado
titleSuffix: Azure Virtual WAN
description: Este artigo descreve como configurar um túnel de utilizador Always On VPN para o seu WAN Virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: 14790d4be91ffd0463436b6ca1d5c8794c102697
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016366"
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
