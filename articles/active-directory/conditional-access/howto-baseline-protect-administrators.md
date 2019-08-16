---
title: A política de linha de base requer MFA para administradores-Azure Active Directory
description: Política de acesso condicional para exigir autenticação multifator para administradores
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b00b061b1763d4b4e7236d8dc9ac1eedf7f923bc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533014"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Política de linha de base: Exigir MFA para administradores (versão prévia)

Os usuários com acesso a contas privilegiadas têm acesso irrestrito ao seu ambiente. Devido à potência que essas contas têm, você deve tratá-las com cuidado especial. Um método comum para melhorar a proteção de contas com privilégios é exigir uma forma mais forte de verificação de conta quando eles são usados para entrar. No Azure Active Directory, você pode obter uma verificação de conta mais forte exigindo a autenticação multifator (MFA).

**Exigir MFA para administradores (versão prévia)** é uma [política de linha de base](concept-baseline-protection.md) que requer MFA sempre que uma das seguintes funções de administrador privilegiadas entra em:

* Administrador Global
* Administrator do SharePoint
* Administrador do Exchange
* Administrador de Acesso Condicional
* Administrador de segurança
* Administrador de assistência técnica/administrador de senha
* Administrador de Faturação
* Administrador de utilizadores

Ao habilitar a política exigir MFA para administradores, as nove funções de administrador acima serão necessárias para se registrar no MFA usando o aplicativo autenticador. Depois que o registro de MFA for concluído, os administradores precisarão executar a MFA toda vez que entrarem.

## <a name="deployment-considerations"></a>Considerações sobre implementação

Como a política **exigir MFA para administradores (versão prévia)** se aplica a todos os administradores críticos, várias considerações precisam ser feitas para garantir uma implantação tranqüila. Essas considerações incluem a identificação de usuários e princípios de serviço no Azure AD que não podem ou não devem executar MFA, bem como aplicativos e clientes usados por sua organização que não dão suporte à autenticação moderna.

### <a name="legacy-protocols"></a>Protocolos herdados

Os protocolos de autenticação herdados (IMAP, SMTP, POP3, etc.) são usados por clientes de email para fazer solicitações de autenticação. Esses protocolos não dão suporte a MFA. A maioria dos comprometimentos de conta vistos pela Microsoft são causadas por atores ruins que realizam ataques contra protocolos herdados tentando ignorar MFA. Para garantir que a MFA seja necessária ao fazer logon em uma conta administrativa e os atores inválidos não possam ignorar a MFA, essa política bloqueia todas as solicitações de autenticação feitas para contas de administrador de protocolos herdados.

> [!WARNING]
> Antes de habilitar essa política, verifique se os administradores não estão usando protocolos de autenticação herdados. Consulte o artigo [como: Bloqueie a autenticação herdada no Azure AD com](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) acesso condicional para obter mais informações.

## <a name="enable-the-baseline-policy"></a>Habilitar a política de linha de base

A política **de linha de base de política: Exigir MFA para administradores (versão prévia** ) vem pré-configurado e será exibido na parte superior quando você navegar para a folha acesso condicional em portal do Azure.

Para habilitar essa política e proteger seus administradores:

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Exigir MFA para administradores (versão prévia**).
1. Defina **habilitar política** para **usar a política imediatamente**.
1. Clique em **Guardar**.

> [!WARNING]
> Houve uma opção para **habilitar automaticamente a política no futuro** quando essa política estava em versão prévia. Removemos essa opção para minimizar o impacto repentino do usuário. Se você selecionou essa opção quando ela estava disponível, não **usar política** agora será selecionada automaticamente. Se quiser usar essa política de linha de base, consulte as etapas acima para habilitá-la.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)
