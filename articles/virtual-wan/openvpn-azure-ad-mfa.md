---
title: 'Habilitar MFA para usuários VPN: autenticação do Azure AD'
description: Habilitar a autenticação multifator para usuários VPN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: fcb60e80189da89b3f634c14582be606307536e6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166625"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Habilitar a MFA (autenticação multifator) do Azure para usuários VPN

Se desejar que os usuários sejam solicitados a fornecer um segundo fator de autenticação antes de conceder acesso, você poderá configurar a MFA (autenticação multifator) do Azure para seu locatário do Azure AD. As etapas neste artigo ajudam você a habilitar um requisito para a verificação em duas etapas.

## <a name="prereq"></a>Forem

O pré-requisito para essa configuração é um locatário do Azure AD configurado usando as etapas em [configurar um locatário](openvpn-azure-ad-tenant.md).

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>Definir configurações de entrada

Na página **VPN do Azure – Propriedades** , defina as configurações de entrada.

1. Definir **habilitado para que os usuários entrem?** para **Sim**. Isso permite que todos os usuários no locatário do AD se conectem à VPN com êxito.
2. Definir **atribuição de usuário necessária?** para **Sim** se você quiser limitar a entrada somente a usuários que têm permissões para a VPN do Azure.
3. Guarde as alterações.

   ![Permissões](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Passos seguintes

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [Configurar a autenticação do Azure ad para a conexão ponto a site com o Azure](virtual-wan-point-to-site-azure-ad.md).
