---
title: Políticas de Proteção de Identidade Azure AD
description: Identificar as três políticas que são habilititados com a Proteção de Identidade
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28a9080ce878e262573adf0b3c79394079c09ca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835973"
---
# <a name="identity-protection-policies"></a>Políticas do Identity Protection

A Azure Ative Directory Identity Protection inclui três políticas predefinidas que os administradores podem escolher para ativar. Estas políticas incluem personalização limitada, mas são aplicáveis à maioria das organizações. Todas as políticas permitem excluir utilizadores como o [acesso de emergência ou contas de administrador de break-glass](../roles/security-emergency-access.md).

![Políticas do Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-ad-mfa-registration-policy"></a>Política de registo de MFA Azure AD

A Proteção de Identidade pode ajudar as organizações a lançar a autenticação multi-factor Azure AD (MFA) usando uma política de acesso condicional que requer registo no início de sação. Ativar esta política é uma ótima maneira de garantir que novos utilizadores na sua organização se registaram para MFA no seu primeiro dia. A autenticação multi-factor é um dos métodos de auto-remediação para eventos de risco dentro da Proteção de Identidade. A auto-remediação permite que os seus utilizadores tomem medidas por si só para reduzir o volume de chamadas helpdesk.

Mais informações sobre a autenticação multi-factor Azure AD podem ser encontradas no artigo, [Como funciona: Autenticação multi-factor Azure AD](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Política de risco do início de sessão

A Proteção de Identidade analisa os sinais de cada s indicado, tanto em tempo real como offline, e calcula uma pontuação de risco com base na probabilidade de o início não ter sido realizado pelo utilizador. Os administradores podem tomar uma decisão com base neste sinal de pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas requerem autenticação de vários fatores.

Se o risco for detetado, os utilizadores podem efetuar a autenticação de vários fatores para se auto-remediar e fechar o evento de sinalização de risco para evitar ruídos desnecessários para os administradores.

> [!NOTE] 
> Os utilizadores devem ter-se registado previamente para autenticação multi-factor Azure AD antes de desencadear a política de risco de inscrição.

### <a name="custom-conditional-access-policy"></a>Política de acesso condicional personalizado

Os administradores também podem optar por criar uma política de acesso condicional personalizada, incluindo o risco de inscrição como condição de atribuição. Mais informações sobre o risco como condição numa política de acesso condicional podem ser encontradas no artigo, [Acesso Condicional: Condições](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Política de risco de acesso condicional personalizado](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Política de risco do utilizador

A Proteção de Identidade pode calcular o que acredita ser normal para o comportamento de um utilizador e usá-lo para basear decisões para o seu risco. O risco do utilizador é um cálculo da probabilidade de uma identidade ter sido comprometida. Os administradores podem tomar uma decisão com base neste sinal de pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas requerem uma alteração de palavra-passe utilizando [o reset da palavra-passe de autosserviço AD Azure](../authentication/howto-sspr-deployment.md).

Se o risco for detetado, os utilizadores podem efetuar a autoassistência de autosserviço reset para auto-remediar e fechar o evento de risco do utilizador para evitar ruídos desnecessários para os administradores.

> [!NOTE] 
> Os utilizadores devem ter-se registado previamente para reiniciar a palavra-passe de autosserviço antes de desencadear a política de risco do utilizador.

## <a name="next-steps"></a>Passos seguintes

- [Ativar o reset da palavra-passe de autosserviço AZure AD](../authentication/howto-sspr-deployment.md)

- [Ativar o Multi-Factor Authentication do Azure AD](../authentication/howto-mfa-getstarted.md)

- [Ativar a política de registo de autenticação multi-factor Azure AD](howto-identity-protection-configure-mfa-policy.md)

- [Ativar políticas de insusição e risco de utilizador](howto-identity-protection-configure-risk-policies.md)
