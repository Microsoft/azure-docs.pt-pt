---
title: Políticas comuns de acesso condicional - Diretório Ativo Azure
description: Políticas de acesso condicional comumente utilizadas para organizações
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdaaed72b85bc3c0196a8897be311efa5fb714e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366348"
---
# <a name="common-conditional-access-policies"></a>Políticas de Acesso Condicional comuns

[Os incumprimentos de segurança](../fundamentals/concept-fundamentals-security-defaults.md) são ótimos para alguns, mas muitas organizações precisam de mais flexibilidade do que oferecem. Por exemplo, muitas organizações precisam da capacidade de excluir contas específicas, como o seu acesso de emergência ou contas de administração de break-glass de políticas de Acesso Condicional que requerem autenticação de vários fatores. Para essas organizações, as políticas comuns referidas neste artigo podem ser utilizadas.

![Políticas de acesso condicionado no portal Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Contas de acesso de emergência

Mais informações sobre contas de acesso de emergência e por que são importantes podem ser encontradas nos seguintes artigos: 

* [Gerir contas de acesso de emergência no AAD](../roles/security-emergency-access.md)
* [Crie uma estratégia resiliente de gestão de controlo de acessos com o Azure Ative Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Políticas típicas implementadas pelas organizações

* [Bloquear a autenticação do legado](howto-conditional-access-policy-block-legacy.md)\*
* [Exigir MFA para administradores](howto-conditional-access-policy-admin-mfa.md)\*
* [Exigir MFA para gestão Azure](howto-conditional-access-policy-azure-management.md)\*
* [Exigir MFA para todos os utilizadores](howto-conditional-access-policy-all-users-mfa.md)\*

\* Estas quatro políticas, quando configuradas em conjunto, imitam a funcionalidade ativada por [incumprimentos de segurança](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Políticas adicionais

* [Acesso condicional baseado no risco de acesso (requer Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Acesso Condicional baseado no risco do utilizador (requer Azure AD Premium P2)](howto-conditional-access-policy-risk-user.md)
* [Exigir localização fidedigna para o registo com MFA](howto-conditional-access-policy-registration.md)
* [Bloquear acesso por localização](howto-conditional-access-policy-location.md)
* [Pedir dispositivo conforme](howto-conditional-access-policy-compliant-device.md)
* [Bloquear o acesso, exceto aplicações específicas](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Passos seguintes

- [Simular o sinal no comportamento utilizando a ferramenta Acesso Condicional E se a ferramenta.](troubleshoot-conditional-access-what-if.md)

- [Utilize o modo apenas de relatório para acesso condicional para determinar o impacto de novas decisões políticas.](concept-conditional-access-report-only.md)
