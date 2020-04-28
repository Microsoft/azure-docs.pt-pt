---
title: Experiências de utilizador com proteção de identidade Azure AD
description: Experiência do utilizador da Proteção de Identidade Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72887003"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Experiências de utilizador com proteção de identidade Azure AD

Com a Proteção de Identidade do Diretório Ativo Azure, pode:

* Exigir que os utilizadores se registem para autenticação multi-factor (MFA)
* Automatizar a reparação de inscrições arriscadas e utilizadores comprometidos

Todas as políticas de Proteção de Identidade têm impacto no sinal de experiência para os utilizadores. Permitir que os utilizadores se registem e utilizem ferramentas como o Azure MFA e o reset de passwords self-service pode diminuir o impacto. Estas ferramentas, juntamente com as opções políticas adequadas, conferem aos utilizadores uma opção de autorreparação quando precisam.

## <a name="multi-factor-authentication-registration"></a>Registo de autenticação de vários fatores

Ativar a política de Proteção de Identidade que exija o registo de autenticação de vários fatores e direciona todos os seus utilizadores, certificar-se-á de que têm a capacidade de utilizar o Azure MFA para se auto-remediarem no futuro. Configurar esta política dá aos seus utilizadores um período de 14 dias onde podem optar por se registar e no final são obrigados a registar-se. A experiência para os utilizadores está descrita abaixo. Mais informações podem ser encontradas na documentação do utilizador final no artigo, [visão geral para verificação de dois fatores e sua conta de trabalho ou escola](../user-help/user-help-two-step-verification-overview.md).

### <a name="registration-interrupt"></a>Interrupção de inscrições

1. No início do início de qualquer aplicação integrada em AD Azure, o utilizador recebe uma notificação sobre a exigência de configurar a conta para autenticação multifactor. Esta política também é desencadeada no Windows 10 out of Box Experience para novos utilizadores com um novo dispositivo.
   
    ![Mais informações necessárias](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Complete os passos guiados para se registar para autenticação de multi-factores Azure e complete o seu início de sessão.

## <a name="risky-sign-in-remediation"></a>Reparação de inscrição arriscada

Quando um administrador configura uma política de riscos de inscrição, os utilizadores afetados são notificados quando tentam iniciar o sessão e desencadear o nível de risco das políticas. 

### <a name="risky-sign-in-self-remediation"></a>Auto-remediação de inscrição arriscada

1. O utilizador é informado de que algo de invulgar foi detetado sobre o seu sessão, como iniciar sessão a partir de um novo local, dispositivo ou app.
   
    ![Algo incomum de pronta](./media/concept-identity-protection-user-experience/120.png)

1. O utilizador é obrigado a provar a sua identidade completando o Azure MFA com um dos seus métodos previamente registados. 

### <a name="risky-sign-in-administrator-unblock"></a>Administrador de inscrição arriscado desbloqueia

Os administradores podem optar por bloquear os utilizadores no momento do sessão, dependendo do seu nível de risco. Para serem desbloqueados, os utilizadores finais devem contactar os seus funcionários de TI, ou podem tentar iniciar sessão a partir de um local ou dispositivo familiar. A autorreparação através da autenticação multifactor não é uma opção neste caso.

![Bloqueado por política de risco de inscrição](./media/concept-identity-protection-user-experience/200.png)

O pessoal de TI pode seguir as instruções na secção [Desbloqueio dos utilizadores](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) para permitir que os utilizadores voltem a entrar.

## <a name="risky-user-remediation"></a>Remediação arriscada dos utilizadores

Quando uma política de risco do utilizador estiver configurada, os utilizadores que satisfaçam a probabilidade de compromisso do nível de risco do utilizador devem passar pelo fluxo de recuperação de compromisso do utilizador antes de poderem iniciar sessão. 

### <a name="risky-user-self-remediation"></a>Auto-remediação do utilizador arriscado

1. O utilizador é informado de que a sua segurança da conta está em risco devido a atividade suspeita ou credenciais vazadas.
   
    ![Remediação](./media/concept-identity-protection-user-experience/101.png)

1. O utilizador é obrigado a provar a sua identidade completando o Azure MFA com um dos seus métodos previamente registados. 
1. Por fim, o utilizador é obrigado a alterar a sua palavra-passe utilizando o reset da palavra-passe self-service, uma vez que outra pessoa pode ter tido acesso à sua conta.

## <a name="risky-sign-in-administrator-unblock"></a>Administrador de inscrição arriscado desbloqueia

Os administradores podem optar por bloquear os utilizadores no momento do sessão, dependendo do seu nível de risco. Para desbloquear, os utilizadores finais devem contactar o seu pessoal de TI. A autorreparação através da autenticação multi-factor e do reset da palavra-passe self-service não é uma opção neste caso.

![Bloqueado pela política de risco do utilizador](./media/concept-identity-protection-user-experience/104.png)

O pessoal de TI pode seguir as instruções na secção [Desbloqueio dos utilizadores](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) para permitir que os utilizadores voltem a entrar.

## <a name="see-also"></a>Consulte também

- [Remediar riscos e desbloquear utilizadores](howto-identity-protection-remediate-unblock.md)

- [Proteção de Identidade do Diretório Ativo Azure](../active-directory-identityprotection.md) 
