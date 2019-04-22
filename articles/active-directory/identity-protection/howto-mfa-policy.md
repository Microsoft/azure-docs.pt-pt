---
title: Como configurar a política de registo de autenticação multifator no Azure Active Directory Identity Protection | Documentos da Microsoft
description: Saiba como configurar a política de registo de multi-factor authentication do Azure AD Identity Protection.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b563b0fb8dc12cbdcdd256a97134f9ff02988915
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698967"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Como: Configurar a política de registo de autenticação multifator

O Azure AD Identity Protection ajuda a gerir o roll-cancelar o registro de autenticação multifator (MFA) ao configurar uma política para exigir o registo do MFA. Este artigo explica o que pode ser utilizada a política para um como configurá-lo.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>O que é a política de registo de autenticação multifator?

Autenticação multifator do Azure é um método de verificação quem é o que requer a utilização de mais do que apenas um nome de utilizador e palavra-passe. Ele fornece uma segunda camada de segurança para inícios de sessão de utilizador e transações. Por ordem para que os utilizadores possam responder a pedidos MFA, eles tem primeiro de registar para a MFA. 

Recomendamos que exigem multi-factor authentication para inícios de sessão de utilizador, porque ele:

- Proporciona uma autenticação segura com uma variedade de opções de verificação simples

- Desempenha um papel fundamental na preparação de sua organização para proteger e recuperar a partir de comprometimentos de conta


Para obter mais detalhes sobre a MFA, consulte [o que é o Azure multi-factor Authentication?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Como posso aceder a política de registo MFA?
   
A política de registo MFA está no **configurar** secção sobre o [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política da MFA](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Definições de política

Ao configurar a política de registo do MFA, tem de definir:

- Os utilizadores e grupos que a política aplica-se a:

    ![Utilizadores e grupos](./media/howto-mfa-policy/11.png)

- O controle que pretende impor requisitos de registo na MFA:  

    ![Access](./media/howto-mfa-policy/12.png)

- O estado da política:

    ![Impor a política](./media/howto-mfa-policy/14.png)


A caixa de diálogo de configuração de política fornece uma opção para calcular o impacto da sua configuração.

![Impacto estimado](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>Experiência de utilizador


Para uma descrição geral da experiência do usuário relacionadas, consulte:

* [Fluxo de registo de autenticação multifator](flows.md#multi-factor-authentication-registration).  
* [Experiências de início de sessão com o Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [descrição geral do Azure AD Identity Protection](overview.md).
