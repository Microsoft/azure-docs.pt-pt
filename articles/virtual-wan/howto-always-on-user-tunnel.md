---
title: Configure um túnel de utilizador VPN sempre ligado
titleSuffix: Azure Virtual WAN
description: Este artigo descreve como configurar um túnel de utilizador Always On VPN para o seu WAN Virtual
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502870"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Configure um túnel de utilizador sempre em VPN para WAN virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Deve criar uma configuração ponto-a-local e editar a atribuição do hub virtual. Consulte as seguintes secções para obter instruções:

* [Criar uma configuração P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Editar a atribuição do hub](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Configure um túnel de utilizador

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, utilize os seguintes passos:

1. Execute o seguinte comando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desligue a ligação e limpe a caixa de **verificação de ligação automaticamente.**

   ![Limpeza](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o WAN Virtual, consulte as [FAQ](virtual-wan-faq.md).
