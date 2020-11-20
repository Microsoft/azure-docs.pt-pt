---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0caa3fc9ed576116347b70df2f5d0c539f1cc52f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991106"
---
|Nome<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar o uso das regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Auditar funções incorporadas como "Proprietário, Contribuidor, Leitor" em vez de funções personalizadas de RBAC, que são propensas a erros. A utilização de funções personalizadas é tratada como uma exceção e requer uma revisão rigorosa e modelação de ameaças |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[As funções de proprietário de subscrição personalizada não devem existir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Esta política garante que não existem funções de proprietário de subscrição personalizadas. |Auditoria, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
