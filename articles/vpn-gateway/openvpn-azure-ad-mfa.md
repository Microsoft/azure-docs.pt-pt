---
title: 'Habilitar MFA para usuários VPN: autenticação do Azure AD'
description: Habilitar a autenticação multifator para usuários VPN
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
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Habilitar a MFA (autenticação multifator) do Azure para usuários VPN

Se desejar que os usuários sejam solicitados a fornecer um segundo fator de autenticação antes de conceder acesso, você poderá configurar a MFA (autenticação multifator) do Azure para seu locatário do Azure AD. As etapas neste artigo ajudam você a habilitar um requisito para a verificação em duas etapas.

## <a name="prereq"></a>Forem

O pré-requisito para essa configuração é um locatário do Azure AD configurado usando as etapas em [configurar um locatário](openvpn-azure-ad-tenant.md).

## <a name="mfa"></a>Abrir a página MFA

1. Inicie sessão no Portal do Azure.
2. Navegue até **Azure Active Directory-> todos os usuários**.
3. Selecione **autenticação multifator** para abrir a página autenticação multifator.

   ![Iniciar sessão](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>Selecionar usuários

1. Na página **autenticação multifator** , selecione os usuários para os quais você deseja habilitar a MFA.
2. Selecione **Ativar**.

   ![Selecione](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>Habilitar autenticação

1. Navegue até **Azure Active Directory-> aplicativos empresariais-> todos os aplicativos**.
2. Na página **aplicativos empresariais – todos os aplicativos** , selecione **VPN do Azure**.

   ![ID do diretório](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Definir configurações de entrada

Na página **VPN do Azure – Propriedades** , defina as configurações de entrada.

1. Definir **habilitado para que os usuários entrem?** para **Sim**. Isso permite que todos os usuários no locatário do AD se conectem à VPN com êxito.
2. Definir **atribuição de usuário necessária?** para **Sim** se você quiser limitar a entrada somente a usuários que têm permissões para a VPN do Azure.
3. Guarde as alterações.

   ![Permissões](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Passos Seguintes

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para conexões VPN P2S](openvpn-azure-ad-client.md).
