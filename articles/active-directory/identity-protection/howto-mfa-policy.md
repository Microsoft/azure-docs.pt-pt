---
title: Como configurar a política de registo de autenticação multifator no Azure Active Directory Identity Protection | Documentos da Microsoft
description: Saiba como configurar a política de registo de multi-factor authentication do Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 434d07163713a139b42a5dbe1664f81dafc2a1ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108941"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Como: Configurar a política de registo de multi-factor Authentication do Azure

O Azure AD Identity Protection ajuda-o a gerir o roll-cancelar o registro de autenticação multifator (MFA) ao configurar uma política de acesso condicional para exigir o registo do MFA, independentemente da aplicação que está a iniciar. Este artigo explica o que a política pode ser utilizada e como configurá-lo.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>O que é a política de registo de multi-factor Authentication do Azure?

O Azure multi-factor Authentication fornece um meio para verificar quem estiver a utilizar mais do que apenas um nome de utilizador e palavra-passe. Ele fornece uma segunda camada de segurança para inícios de sessão do utilizador. Por ordem para que os utilizadores possam responder a pedidos MFA, tem primeiro estabelecimentos de multi-factor Authentication.

Recomendamos que exigem multi-factor Authentication para inícios de sessão de utilizador, porque ele:

- Proporciona uma autenticação segura com uma variedade de opções de verificação simples
- Desempenha um papel fundamental na preparação de sua organização para proteger e recuperar a partir de eventos de risco no Identity Protection

Para obter mais detalhes sobre a MFA, consulte [o que é o Azure multi-factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Como posso aceder a política de registo?

A política de registo MFA está no **configurar** secção sobre o [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Política da MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Definições de política

Ao configurar a política de registo do MFA, tem de efetuar as seguintes alterações de configuração:

- Os utilizadores e grupos que se aplica a política. Lembre-se de excluir da sua organização [contas de acesso de emergência](../users-groups-roles/directory-emergency-access.md).

    ![Utilizadores e grupos](./media/howto-mfa-policy/11.png)

- O controle que deseja impor - **registo exigir MFA do Azure**

    ![Access](./media/howto-mfa-policy/12.png)

- Impor a política deve ser definida como **no**.

    ![Impor a política](./media/howto-mfa-policy/14.png)

- **Guardar** sua política

## <a name="user-experience"></a>Experiência de utilizador

Para uma descrição geral da experiência do usuário relacionadas, consulte:

- [Fluxo de registo de autenticação multifator](flows.md#multi-factor-authentication-registration).  
- [Experiências de início de sessão com o Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [descrição geral do Azure AD Identity Protection](overview.md).
