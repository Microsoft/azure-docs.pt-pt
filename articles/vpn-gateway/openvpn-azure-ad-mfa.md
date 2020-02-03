---
title: 'Habilitar MFA para usuários VPN: autenticação do Azure AD'
description: Habilitar a autenticação multifator para usuários VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964739"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Habilitar a MFA (autenticação multifator) do Azure para usuários VPN

Se pretender que os utilizadores sejam solicitados para um segundo fator de autenticação antes de conceder acesso, pode configurar a Autenticação Multifactor Azure (MFA) por utilizador ou alavancar a Autenticação Multi-Factor (MFA) através do [Acesso Condicional](../active-directory/conditional-access/overview.md) para um controlo mais fino. Configurar a autenticação de vários fatores por utilizador pode ser ativado sem custos adicionais, no entanto, ao ativar o MFA por utilizador, o utilizador será solicitado para autenticação de segundo fator contra todas as aplicações ligadas ao inquilino Da Azure. O Acesso Condicional permitirá um controlo mais fino dos grãos sobre a forma como um segundo fator deve ser promovido e pode permitir a atribuição de MFA apenas VPN e não outras aplicações ligadas ao inquilino da AD Azure.

## <a name="enableauth"></a>Habilitar autenticação

1. Navegue até **Azure Active Directory-> aplicativos empresariais-> todos os aplicativos**.
2. Na página **aplicativos empresariais – todos os aplicativos** , selecione **VPN do Azure**.

   ![ID do diretório](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Definir configurações de entrada

Na página **VPN do Azure – Propriedades** , defina as configurações de entrada.

1. Definir **habilitado para que os usuários entrem?** para **Sim**. Isso permite que todos os usuários no locatário do AD se conectem à VPN com êxito.
2. Definir **atribuição de usuário necessária?** para **Sim** se você quiser limitar a entrada somente a usuários que têm permissões para a VPN do Azure.
3. Guarde as alterações.

   ![Permissões](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>Opção 1 - Ativar a autenticação multi-factor (MFA) através do acesso condicional

O Acesso Condicional permite um controlo de acesso de grãos finos por aplicação.  Por favor, note que para alavancar o Acesso Condicional, deve ter O Azure AD Premium 1 ou um maior licenciamento aplicado aos utilizadores que estarão sujeitos às regras de Acesso Condicional.

1. Nas **aplicações da Enterprise - Todas as aplicações,** **selecione Azure VPN,** selecione **Acesso Condicional,** e clique em **Nova política**.
2. Em Utilizadores e grupos, no separador *Incluir* ver **selecione utilizadores e grupos,** verifique **utilizadores e grupos,** e selecione um grupo ou conjunto de utilizadores que deverão estar sujeitos a MFA.  Clique em **Concluído**.
![Atribuições](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Em **Grant**, verifique **o acesso ao Grant,** verifique **Exigir autenticação de vários fatores,** verificar **Exigir todos os controlos selecionados**e clicar no botão **Select.**
![Grant access - MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Verifique a **política ativação** e clique no botão **Criar.**
![enable policy](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>Opção 2 - Ativar a autenticação de vários fatores (MFA) por utilizador

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>Passos seguintes

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para conexões VPN P2S](openvpn-azure-ad-client.md).
