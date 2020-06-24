---
title: Configure um túnel de utilizador VPN sempre ligado
titleSuffix: Azure VPN Gateway
description: Este artigo descreve como configurar um túnel de utilizador Always On VPN para o seu gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8cfe1d6434c0f5765196776ae9f6712fe14c52a3
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84984128"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurar um túnel de utilizador VPN AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurar o gateway

 Utilize as instruções no [Configure um artigo de ligação VPN ponto-a-local para](vpn-gateway-howto-point-to-site-resource-manager-portal.md) configurar o gateway VPN para utilizar o IKEv2 e a autenticação baseada em certificados.

## <a name="configure-a-user-tunnel"></a>Configure um túnel de utilizador

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, utilize os seguintes passos:

1. Execute o seguinte comando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desligue a ligação e limpe a caixa de verificação **do Connect automaticamente.**

   ![Limpeza](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Passos seguintes

Para resolver problemas de ligação que possam ocorrer, consulte [os problemas de ligação ponto-a-local do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
