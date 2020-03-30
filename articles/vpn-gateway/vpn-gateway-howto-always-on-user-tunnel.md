---
title: Configure um túnel de utilizador VPN sempre ligado
titleSuffix: Azure VPN Gateway
description: Este artigo descreve como configurar um túnel de utilizador Always On VPN para o seu gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502267"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurar um túnel de utilizador VPN AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurar o gateway

 Utilize as instruções no Configure um artigo [de ligação VPN ponto-a-local](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para configurar o gateway VPN para utilizar o IKEv2 e a autenticação baseada em certificados.

## <a name="configure-a-user-tunnel"></a>Configure um túnel de utilizador

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, utilize os seguintes passos:

1. Execute o seguinte comando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desligue a ligação e limpe a caixa de **verificação de ligação automaticamente.**

   ![Limpeza](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Passos seguintes

Para resolver problemas de ligação que possam ocorrer, consulte os problemas de [ligação ponto-a-local do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
