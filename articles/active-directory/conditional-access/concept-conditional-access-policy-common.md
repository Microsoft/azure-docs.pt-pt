---
title: Políticas de acesso condicional comum-Azure Active Directory
description: Políticas de acesso condicional comumente usadas para organizações
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf338ad0c555038cd99c3604d69ab80371d6ea22
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74804979"
---
# <a name="common-conditional-access-policies"></a>Políticas de Acesso Condicional comuns

As políticas de proteção de linha de base são ótimas, mas muitas organizações precisam de mais flexibilidade do que oferecem. Por exemplo, muitas organizações precisam da capacidade de excluir contas específicas como suas contas de acesso de emergência ou de administração de interrupções de políticas de acesso condicional que exigem autenticação multifator. Para essas organizações, as políticas comuns referenciadas neste artigo podem ser usadas.

![Políticas de acesso condicional no portal do Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Contas de acesso de emergência

Mais informações sobre contas de acesso de emergência e por que elas são importantes podem ser encontradas nos seguintes artigos: 

* [Gerenciar contas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Crie uma estratégia de gerenciamento de controle de acesso resiliente com Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Políticas típicas implantadas por organizações

* [Exigir MFA para administradores](howto-conditional-access-policy-admin-mfa.md)
* [Exigir MFA para gerenciamento do Azure](howto-conditional-access-policy-azure-management.md)
* [Exigir MFA para todos os usuários](howto-conditional-access-policy-all-users-mfa.md)
* [Bloquear autenticação herdada](howto-conditional-access-policy-block-legacy.md)
* [Acesso condicional com base em risco (requer Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Exigir local confiável para o registro de MFA](howto-conditional-access-policy-registration.md)
* [Bloquear o acesso por local](howto-conditional-access-policy-location.md)
* [Exigir dispositivo em conformidade](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>Passos seguintes

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
