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
ms.openlocfilehash: bf9dbd0cef19ad54ba6c3b58f2b9b3071b98bd93
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371006"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurar um túnel de utilizador VPN AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configure o portal

 Utilize as instruções no Configure um artigo [de ligação VPN ponto-a-local](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para configurar o gateway VPN para utilizar o IKEv2 e a autenticação baseada em certificados.

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
