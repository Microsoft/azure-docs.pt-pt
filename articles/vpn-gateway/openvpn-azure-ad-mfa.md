---
title: 'Enable MFA for VPN users: Azure AD authentication| Microsoft Docs'
description: Enable multi-factor authentication for VPN users
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: b09b66ee7088b879b01a8ea0ee2068267034d9b6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209512"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>Enable Multi-Factor Authentication (MFA) for VPN users

You can enable MFA for users in your Azure AD tenant so that users are prompted for a second factor authentication before access is granted.

> [!NOTE]
> Prerequisite: You have configured an Azure AD tenant as described in the "Configure a tenant" document.
>

#### <a name="tenant"></a>1. Login to the Azure portal and navigate to **Azure Active Directory** , **All users** and click on **Multi-Factor Authentication**


   ![New Azure AD tenant](./media/openvpn-azure-ad-mfa/mfa1.jpg)

#### <a name="users"></a>2. Select the user(s) that you want to enable MFA for and click **enable**

   ![New Azure AD tenant](./media/openvpn-azure-ad-mfa/mfa2.jpg)

#### <a name="enable-authentication"></a>3. Navigate to **Azure Active Directory** , **Enterprise applications**, **All applications**  and click on **Azure VPN**


   ![Directory ID](./media/openvpn-azure-ad-mfa/user1.jpg)

#### <a name="users"></a>4. Make sure the **Enabled for users to sign-in?** is set to yes. If you want just the users that have permissions to the Azure VPN to be able to login then Set **User assignment required?** to yes as well otherwise all users in the AD tenant will be able to connect to VPN successfully.

   ![Permissões](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Passos seguintes

In order to connect to your virtual network, you must create and configure a VPN client profile. See [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).
