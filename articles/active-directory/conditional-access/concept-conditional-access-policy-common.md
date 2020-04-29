---
title: Políticas comuns de acesso condicional - Diretório Ativo Azure
description: Políticas de acesso condicional comumente utilizadas para organizações
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295311"
---
# <a name="common-conditional-access-policies"></a>Políticas de Acesso Condicional comuns

[Os incumprimentos de segurança](../fundamentals/concept-fundamentals-security-defaults.md) são ótimos para alguns, mas muitas organizações precisam de mais flexibilidade do que oferecem. Por exemplo, muitos precisam da capacidade de excluir contas específicas como o seu acesso de emergência ou contas de administração de vidro sinuoso de políticas de acesso condicional que requerem a autenticação de vários fatores. Para essas organizações, as políticas comuns referidas neste artigo podem ser de uso.

![Políticas de Acesso Condicional no portal Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Contas de acesso de emergência

Podem ser encontradas mais informações sobre as contas de acesso de emergência e por que razão são importantes nos seguintes artigos: 

* [Gerir contas de acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Criar uma estratégia resiliente de gestão de controlo de acesso com o Diretório Ativo azure](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Políticas típicas implementadas por organizações

* [Autenticação do legado do bloco](howto-conditional-access-policy-block-legacy.md)\*
* [Exigir MFA para administradores](howto-conditional-access-policy-admin-mfa.md)\*
* [Exigir MFA para a gestão do Azure](howto-conditional-access-policy-azure-management.md)\*
* [Exigir MFA para todos os utilizadores](howto-conditional-access-policy-all-users-mfa.md)\*

\*Estas quatro políticas quando configuradas em conjunto, imitariam a funcionalidade ativada por [predefinições](../fundamentals/concept-fundamentals-security-defaults.md)de segurança .

## <a name="additional-policies"></a>Políticas adicionais

* [Acesso Condicional baseado no risco (Requer o Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Exigir localização fidedigna para o registo com MFA](howto-conditional-access-policy-registration.md)
* [Bloquear acesso por localização](howto-conditional-access-policy-location.md)
* [Pedir dispositivo conforme](howto-conditional-access-policy-compliant-device.md)
* [Bloquear acesso, exceto aplicações específicas](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Passos seguintes

- [Simular o sinal de comportamento utilizando a ferramenta de acesso condicional E Se.](troubleshoot-conditional-access-what-if.md)

- [Utilize o modo de acesso condicional apenas para o relatório para determinar o impacto das novas decisões políticas.](concept-conditional-access-report-only.md)
