---
title: Políticas de Azure AD Identity Protection
description: Identificando as três políticas que são habilitadas com a proteção de identidade
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
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887419"
---
# <a name="identity-protection-policies"></a>Políticas de proteção de identidade

Azure Active Directory Identity Protection inclui três políticas padrão que os administradores podem optar por habilitar. Essas políticas incluem personalização limitada, mas são aplicáveis à maioria das organizações. Todas as políticas permitem a exclusão de usuários, como [contas de administrador de acesso de emergência ou](../users-groups-roles/directory-emergency-access.md)de usuário de vidro.

![Políticas de proteção de identidade](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Política de registro do Azure MFA

A proteção de identidade pode ajudar as organizações a distribuir a MFA (autenticação multifator) do Azure usando uma política de acesso condicional que requer registro na entrada. Habilitar essa política é uma ótima maneira de garantir que novos usuários em sua organização tenham se registrado para MFA no primeiro dia. A autenticação multifator é um dos métodos de AutoCorreção para eventos de risco dentro da proteção de identidade. A AutoCorreção permite que os usuários tomem providências por conta própria para reduzir o volume de chamadas da assistência técnica.

Mais informações sobre a autenticação multifator do Azure podem ser encontradas no artigo [como funciona: autenticação multifator do Azure](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Política de risco de entrada

A proteção de identidade analisa os sinais de cada entrada, tanto em tempo real quanto offline, e calcula uma pontuação de risco com base na probabilidade de que a entrada não tenha sido executada pelo usuário. Os administradores podem tomar uma decisão com base nesse sinal de Pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir acesso ou permitir acesso, mas exigem a autenticação multifator.

Se o risco for detectado, os usuários poderão executar a autenticação multifator para remediar e fechar o evento de entrada arriscado para evitar ruídos desnecessários para os administradores.

> [!NOTE] 
> Os usuários devem ter registrado anteriormente para a autenticação multifator do Azure antes de disparar a política de risco de entrada.

### <a name="custom-conditional-access-policy"></a>Política de acesso condicional personalizada

Os administradores também podem optar por criar uma política de acesso condicional personalizada, incluindo o risco de entrada como uma condição de atribuição. Mais informações sobre o acesso condicional podem ser encontradas no artigo [o que é acesso condicional?](../conditional-access/overview.md)

![Política de risco de entrada de acesso condicional personalizado](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Política de risco do usuário

A proteção de identidade pode calcular o que acredita ser normal para o comportamento de um usuário e usá-lo para basear as decisões em risco. O risco do usuário é um cálculo da probabilidade de que uma identidade tenha sido comprometida. Os administradores podem tomar uma decisão com base nesse sinal de Pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir acesso ou permitir acesso, mas exigem uma alteração de senha usando a [redefinição de senha de autoatendimento do Azure ad](../authentication/howto-sspr-deployment.md).

Se o risco for detectado, os usuários poderão executar a redefinição de senha de autoatendimento para corrigir automaticamente e fechar o evento de risco do usuário para evitar ruídos desnecessários para os administradores.

> [!NOTE] 
> Os usuários devem ter sido previamente registrados para redefinição de senha de autoatendimento antes de disparar a política de risco do usuário.

## <a name="next-steps"></a>Passos seguintes

- [Habilitar redefinição de senha de autoatendimento do Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitar a autenticação multifator do Azure](../authentication/howto-mfa-getstarted.md)

- [Habilitar a política de registro da autenticação multifator do Azure](howto-identity-protection-configure-mfa-policy.md)

- [Habilitar as políticas de entrada e de risco do usuário](howto-identity-protection-configure-risk-policies.md)
