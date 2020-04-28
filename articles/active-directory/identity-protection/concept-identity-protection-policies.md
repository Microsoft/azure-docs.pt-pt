---
title: Políticas de Proteção de Identidade da AD Azure
description: Identificação das três políticas que estão habilitadas com proteção de identidade
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
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72887419"
---
# <a name="identity-protection-policies"></a>Políticas do Identity Protection

A Azure Ative Directory Identity Protection inclui três políticas padrão que os administradores podem escolher para ativar. Estas políticas incluem personalização limitada, mas são aplicáveis à maioria das organizações. Todas as políticas permitem excluir utilizadores como o seu [acesso de emergência ou contas de administrador de vidro partido](../users-groups-roles/directory-emergency-access.md).

![Políticas do Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Política de registo do Azure MFA

A Proteção de Identidade pode ajudar as organizações a lançar a Autenticação Multi-Factor Azure (MFA) utilizando uma política de Acesso Condicional que exige o registo no início do sessão. Ativar esta política é uma ótima maneira de garantir que novos utilizadores da sua organização se registaram para mfa no seu primeiro dia. A autenticação multifactor é um dos métodos de autorreparação para eventos de risco no âmbito da Proteção de Identidade. A autorreparação permite que os seus utilizadores tomem medidas por si próprios para reduzir o volume de chamadas de helpdesk.

Mais informações sobre a autenticação de multifactor azure podem ser encontradas no artigo, [Como funciona: Autenticação Multi-Factor Azure](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Política de risco de inscrição

A Identity Protection analisa sinais de cada entrada, em tempo real e offline, e calcula uma pontuação de risco com base na probabilidade de o inato não ter sido realizado pelo utilizador. Os administradores podem tomar uma decisão com base neste sinal de pontuação de risco para fazer cumprir os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas exigem a autenticação de vários fatores.

Se o risco for detetado, os utilizadores podem efetuar a autenticação de vários fatores para auto-remediar e fechar o sinal de risco para evitar ruídos desnecessários para os administradores.

> [!NOTE] 
> Os utilizadores devem ter registado previamente a autenticação de multifactor estonteante antes de desencadear a política de risco de entrada.

### <a name="custom-conditional-access-policy"></a>Política de acesso condicional personalizado

Os administradores também podem optar por criar uma política personalizada de Acesso Condicional, incluindo o risco de inscrição como condição de atribuição. Mais informações sobre acesso condicional podem ser encontradas no artigo, [O que é acesso condicional?](../conditional-access/overview.md)

![Política de risco de acesso condicional personalizado](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Política de risco do utilizador

A Proteção de Identidade pode calcular o que acredita ser normal para o comportamento de um utilizador e usá-la para basear decisões para o seu risco. O risco do utilizador é um cálculo de probabilidade de uma identidade ter sido comprometida. Os administradores podem tomar uma decisão com base neste sinal de pontuação de risco para fazer cumprir os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas exigem uma alteração de senha usando o reset da [palavra-passe de autosserviço Daa Azure](../authentication/howto-sspr-deployment.md).

Se o risco for detetado, os utilizadores podem efetuar o reset da palavra-passe self-service para auto-remediar e fechar o evento de risco do utilizador para evitar ruídos desnecessários para os administradores.

> [!NOTE] 
> Os utilizadores devem ter registado previamente para redefinir a palavra-passe de self-service antes de desencadear a política de risco do utilizador.

## <a name="next-steps"></a>Passos seguintes

- [Ativar o reset da palavra-passe autosserviço Azure AD](../authentication/howto-sspr-deployment.md)

- [Ativar a Multi-Factor Authentication do Azure](../authentication/howto-mfa-getstarted.md)

- [Ativar a política de registo de autenticação multi-factor azure](howto-identity-protection-configure-mfa-policy.md)

- [Ativar políticas de risco de inscrição e utilizador](howto-identity-protection-configure-risk-policies.md)
