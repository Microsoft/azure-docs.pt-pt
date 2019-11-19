---
title: 'Habilitar MFA para usuários VPN: autenticação do Azure AD | Microsoft Docs'
description: Habilitar a autenticação multifator para usuários VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 3f407ecfcbfdff72b79bfe6b5bc3e6b7606b1cf0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174632"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>Habilitar a MFA (autenticação multifator) para usuários VPN

Você pode habilitar a MFA para usuários em seu locatário do Azure AD para que os usuários sejam solicitados a fornecer uma autenticação de segundo fator antes que o acesso seja concedido.

> [! Pré-requisito] você configurou um locatário do Azure AD conforme descrito no documento "configurar um locatário".
>

### <a name="tenant"></a>1. faça logon no portal do Azure e navegue até **Azure Active Directory** , **todos os usuários** e clique em **autenticação multifator**


   ![Novo locatário do Azure AD](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. Selecione os usuários para os quais você deseja habilitar a MFA e clique em **habilitar**

   ![Novo locatário do Azure AD](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. Navegue até **Azure Active Directory** , **aplicativos empresariais**, **todos os aplicativos** e clique em **VPN do Azure**


   ![ID do diretório](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. Verifique se o **habilitado para que os usuários entrem?** está definido como Sim. Se você quiser apenas os usuários que têm permissões para a VPN do Azure serem capazes de fazer logon, defina a **atribuição de usuário necessária?** para Sim, caso contrário, todos os usuários no locatário do AD poderão se conectar à VPN com êxito.

   ![Permissões](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Passos seguintes

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para conexões VPN P2S](openvpn-azure-ad-client.md).
