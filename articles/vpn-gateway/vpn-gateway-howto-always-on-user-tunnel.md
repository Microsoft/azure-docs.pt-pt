---
title: Configurar um túnel de utilizador VPN Always-On
titleSuffix: Azure VPN Gateway
description: Este artigo descreve como configurar um túnel de utilizador Always On VPN para o seu gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 99d21222a62ed6b27a6a1b2a73b704f4cb26457b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435801"
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
