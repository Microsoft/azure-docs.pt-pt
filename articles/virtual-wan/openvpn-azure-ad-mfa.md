---
title: Ativar O MFA para utilizadores VPN utilizando a autenticação AZure AD
description: Saiba como ativar a autenticação multi-factor Azure AD (MFA) para utilizadores VPN utilizando a autenticação Azure AD.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e8d90653372b78aad78fad66e4cde21bd2ab81ee
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "95995624"
---
# <a name="enable-azure-ad-multi-factor-authentication-mfa-for-vpn-users-by-using-azure-ad-authentication"></a>Ativar a autenticação multi-factor Azure AD (MFA) para utilizadores VPN utilizando a autenticação Azure AD

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Ative a autenticação

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Configurar definições de inscrição

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Opção 1 - Por acesso ao utilizador

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Opção 2 - Acesso Condicional

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Passos seguintes

Para se ligar à sua rede virtual, tem de criar e configurar um perfil de cliente VPN. Consulte [a autenticação AD configurada para a ligação ponto-a-local ao Azure](virtual-wan-point-to-site-azure-ad.md).
