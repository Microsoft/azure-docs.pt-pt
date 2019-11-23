---
title: 'Enable MFA for VPN users: Azure AD authentication'
description: Enable multi-factor authentication for VPN users
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 7f05b850a0d886ac0df5c542de647f91fe62eb05
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382225"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Enable Azure Multi-Factor Authentication (MFA) for VPN users

If you want users to be prompted for a second factor of authentication before granting access, you can configure Azure Multi-Factor Authentication (MFA) for your Azure AD tenant. The steps in this article help you enable a requirement for two-step verification.

## <a name="prereq"></a>Prerequisite

The prerequisite for this configuration is a configured Azure AD tenant using the steps in [Configure a tenant](openvpn-azure-ad-tenant.md).

## <a name="mfa"></a>Open the MFA page

1. Inicie sessão no Portal do Azure.
2. Navigate to **Azure Active Directory -> All users**.
3. Select **Multi-Factor Authentication** to open the multi-factor authentication page.

   ![Iniciar sessão](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a> Select users

1. On the **multi-factor authentication** page, select the user(s) for which you want to enable MFA.
2. Selecione **Ativar**.

   ![Selecione](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>Enable authentication

1. Navigate to **Azure Active Directory  -> Enterprise applications -> All applications**.
2. On the **Enterprise applications - All applications** page, select **Azure VPN**.

   ![Directory ID](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a> Configure sign-in settings

On the **Azure VPN - Properties** page, configure sign-in settings.

1. Set **Enabled for users to sign-in?** to **Yes**. This allows all users in the AD tenant to connect to the VPN successfully.
2. Set **User assignment required?** to **Yes** if you want to limit sign-in to only users that have permissions to the Azure VPN.
3. Guarde as alterações.

   ![Permissões](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Passos seguintes

To connect to your virtual network, you must create and configure a VPN client profile. See [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).
