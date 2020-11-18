---
title: Experiências do utilizador com a Azure AD Identity Protection
description: Experiência do utilizador da Azure AD Identity Protection
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
ms.openlocfilehash: 4d4897b9187caab50be4db75bbc0af03e3d35aa4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835990"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Experiências do utilizador com a Azure AD Identity Protection

Com a Azure Ative Directory Identity Protection, pode:

* Exigir que os utilizadores se registem para autenticação multi-factor Azure AD (MFA)
* Automatizar a remediação de insuflações arriscadas e utilizadores comprometidos

Todas as políticas de Proteção de Identidade têm impacto no sinal de experiência para os utilizadores. Permitir que os utilizadores se registem e utilizem ferramentas como o Azure AD MFA e o reset da palavra-passe de autosserviço pode diminuir o impacto. Estas ferramentas, juntamente com as opções políticas apropriadas, dão aos utilizadores uma opção de auto-remediação quando precisam dela.

## <a name="multi-factor-authentication-registration"></a>Registo de autenticação multi-factor

Permitindo a política de Proteção de Identidade que requer registo de autenticação multi-factor e direcione todos os seus utilizadores, irá certificar-se de que eles têm a capacidade de utilizar o Azure AD MFA para se auto-remediar no futuro. Configurar esta política confere aos seus utilizadores um período de 14 dias onde podem optar por se registar e no final são obrigados a registar-se. A experiência para os utilizadores está delineada abaixo. Mais informações podem ser encontradas na documentação do utilizador final no artigo, [Visão geral para verificação de dois fatores e a sua conta de trabalho ou escola.](../user-help/multi-factor-authentication-end-user-first-time.md)

### <a name="registration-interrupt"></a>Interrupção de inscrições

1. Ao iniciar sação em qualquer aplicação integrada a Azure AD, o utilizador recebe uma notificação sobre a obrigatoriedade de configurar a conta para a autenticação de vários fatores. Esta política também é ativada no Windows 10 out of Box Experience para novos utilizadores com um novo dispositivo.
   
    ![Mais informações necessárias](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Preencha as etapas guiadas para se registar para autenticação multi-factor Azure AD e completar o seu registo.

## <a name="risky-sign-in-remediation"></a>Remediação de inscrição arriscada

Quando um administrador configura uma política para riscos de inscrição, os utilizadores afetados são notificados quando tentam iniciar sedificação e desencadear o nível de risco das políticas. 

### <a name="risky-sign-in-self-remediation"></a>Auto-remediação de auto-remediação de assinatura arriscada

1. O utilizador é informado de que algo de anormal foi detetado sobre a sua sessão de sessão, como iniciar sessão a partir de um novo local, dispositivo ou app.
   
    ![Algo incomum](./media/concept-identity-protection-user-experience/120.png)

1. O utilizador é obrigado a provar a sua identidade completando o Azure AD MFA com um dos seus métodos previamente registados. 

### <a name="risky-sign-in-administrator-unblock"></a>Desbloqueio de administrador de inscrição arriscado

Os administradores podem optar por bloquear os utilizadores após a inscrição, dependendo do seu nível de risco. Para serem desbloqueados, os utilizadores finais devem contactar o seu pessoal de TI, ou podem tentar iniciar sessão a partir de um local ou dispositivo familiar. A autorreparação através da autenticação multi-factor não é uma opção neste caso.

![Bloqueado pela política de risco de inscrição](./media/concept-identity-protection-user-experience/200.png)

O pessoal de TI pode seguir as instruções na secção [Desbloquear os utilizadores](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) para permitir que os utilizadores voltem a entrar.

## <a name="risky-user-remediation"></a>Remediação arriscada do utilizador

Quando uma política de risco do utilizador foi configurada, os utilizadores que satisfaçam o nível de risco do utilizador de um compromisso devem passar pelo fluxo de recuperação de compromisso do utilizador antes de poderem iniciar snu. 

### <a name="risky-user-self-remediation"></a>Auto-remediação do utilizador arriscado

1. O utilizador é informado de que a segurança da sua conta está em risco devido a atividades suspeitas ou credenciais vazadas.
   
    ![Remediação](./media/concept-identity-protection-user-experience/101.png)

1. O utilizador é obrigado a provar a sua identidade completando o Azure AD MFA com um dos seus métodos previamente registados. 
1. Por fim, o utilizador é obrigado a alterar a sua palavra-passe utilizando a redefinição da palavra-passe de autosserviço, uma vez que outra pessoa pode ter tido acesso à sua conta.

## <a name="risky-sign-in-administrator-unblock"></a>Desbloqueio de administrador de inscrição arriscado

Os administradores podem optar por bloquear os utilizadores após a inscrição, dependendo do seu nível de risco. Para ser desbloqueado, os utilizadores finais devem contactar o seu pessoal de TI. A autorreparação através da autenticação de vários fatores e do reset da palavra-passe de autosserviço não é uma opção neste caso.

![Bloqueado pela política de risco do utilizador](./media/concept-identity-protection-user-experience/104.png)

O pessoal de TI pode seguir as instruções na secção [Desbloquear os utilizadores](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) para permitir que os utilizadores voltem a entrar.

## <a name="see-also"></a>Ver também

- [Remediar riscos e desbloquear utilizadores](howto-identity-protection-remediate-unblock.md)

- [Proteção de Identidade do Diretório Ativo Azure](./overview-identity-protection.md)