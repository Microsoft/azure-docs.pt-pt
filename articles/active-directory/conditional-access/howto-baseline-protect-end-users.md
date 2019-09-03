---
title: Proteção de usuário final da política de linha de base (versão prévia)-Azure Active Directory
description: Política de acesso condicional para exigir autenticação multifator para usuários
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
ms.openlocfilehash: 0f1a2e0bad39b54edc153416e4120bbc6912578c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125455"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Política de linha de base: Proteção do usuário final (versão prévia)

Tendemos a imaginar que as contas de administrador são as únicas contas que precisam de proteção com a MFA (autenticação multifator). Os administradores têm acesso amplo a informações confidenciais e podem fazer alterações nas configurações de toda a assinatura. No entanto, os atores ruins tendem a direcionar os usuários finais. Depois de obter acesso, esses atores inválidos podem solicitar acesso a informações privilegiadas em nome do titular da conta original ou baixar o diretório inteiro para executar um ataque de phishing em toda a organização. Um método comum para melhorar a proteção para todos os usuários é exigir uma forma mais forte de verificação de conta, como a MFA (autenticação multifator).

Para obter um equilíbrio razoável de segurança e usabilidade, os usuários não devem ser solicitados a cada vez que entrarem. As solicitações de autenticação que refletem o comportamento normal do usuário, como a entrada do mesmo dispositivo no mesmo local, têm uma baixa chance de comprometimento. Somente as entradas consideradas arriscadas e mostrar características de um ator inadequado devem ser solicitadas com os desafios de MFA.

A proteção do usuário final é uma política de [linha de base](concept-baseline-protection.md) de MFA baseada em risco que protege todos os usuários em um diretório, incluindo todas as funções de administrador. A habilitação dessa política exige que todos os usuários se registrem no MFA usando o aplicativo autenticador. Os usuários podem ignorar o prompt de registro de MFA por 14 dias, após o qual eles serão impedidos de entrar até que se registrem no MFA. Depois de registrado para MFA, os usuários serão solicitados a receber MFA somente durante tentativas de entrada arriscadas. As contas de usuário comprometidas são bloqueadas até que sua senha seja redefinida e as detecções de risco tenham sido descartadas.

> [!NOTE]
> Essa política se aplica a todos os usuários, incluindo contas de convidado e será avaliada ao fazer logon em todos os aplicativos.

## <a name="recovering-compromised-accounts"></a>Recuperando contas comprometidas

Para ajudar a proteger nossos clientes, o serviço de credenciamento vazado da Microsoft encontra pares de nome de usuário/senha disponíveis publicamente. Se eles corresponderem a um de nossos usuários, ajudaremos a proteger essa conta imediatamente. Os usuários identificados como tendo uma credencial vazada são confirmados comprometidos. Esses usuários serão impedidos de entrar até que sua senha seja redefinida.

Os usuários com uma licença de Azure AD Premium podem restaurar o acesso por meio de redefinição de senha por autoatendimento (SSPR) se a funcionalidade estiver habilitada em seu diretório. Os usuários sem uma licença Premium que se tornam bloqueados devem entrar em contato com um administrador para executar uma redefinição manual de senha e ignorar a detecção de risco de usuário sinalizado.

### <a name="steps-to-unblock-a-user"></a>Etapas para desbloquear um usuário

Confirme se o usuário foi bloqueado pela política examinando os logs de entrada do usuário.

1. Um administrador precisa entrar no **portal do Azure** e navegar até **Azure Active Directory** > **usuários** > clicar no nome do usuário e navegar até as entradas.
1. Para iniciar a redefinição de senha em um usuário bloqueado, um administrador precisa navegar até **Azure Active Directory** > **usuários sinalizados para risco**
1. Clique no usuário cuja conta está bloqueada para exibir informações sobre a atividade de entrada recente do usuário.
1. Clique em Redefinir senha para atribuir uma senha temporária que deve ser alterada no próximo logon.
1. Clique em ignorar todos os eventos para redefinir a pontuação de risco do usuário.

Agora, o usuário pode entrar, redefinir sua senha e acessar o aplicativo.

## <a name="deployment-considerations"></a>Considerações sobre implementação

Como a política de **proteção do usuário final** se aplica a todos os usuários em seu diretório, várias considerações precisam ser feitas para garantir uma implantação tranqüila. Essas considerações incluem a identificação de usuários e princípios de serviço no Azure AD que não podem ou não devem executar MFA, bem como aplicativos e clientes usados por sua organização que não dão suporte à autenticação moderna.

### <a name="legacy-protocols"></a>Protocolos herdados

Os protocolos de autenticação herdados (IMAP, SMTP, POP3, etc.) são usados por clientes de email para fazer solicitações de autenticação. Esses protocolos não dão suporte a MFA.  A maioria dos comprometimentos de conta vistos pela Microsoft são causadas por atores ruins que realizam ataques contra protocolos herdados tentando ignorar MFA. Para garantir que a MFA seja necessária ao fazer logon em uma conta e que os atores inválidos não possam ignorar a MFA, essa política bloqueia todas as solicitações de autenticação feitas para contas de administrador de protocolos herdados.

> [!WARNING]
> Antes de habilitar essa política, verifique se os usuários não estão usando protocolos de autenticação herdados. Consulte o artigo [como: Bloqueie a autenticação herdada no Azure AD com](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) acesso condicional para obter mais informações.

## <a name="enable-the-baseline-policy"></a>Habilitar a política de linha de base

A política **de linha de base de política: A proteção do usuário final (** versão prévia) vem pré-configurada e aparecerá na parte superior quando você navegar até a folha acesso condicional em portal do Azure.

Para habilitar essa política e proteger seus usuários:

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Proteção do usuário final (versão**prévia).
1. Defina **habilitar política** para **usar a política imediatamente**.
1. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)
