---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: e8b79c8a2e83a77f1f0de344306b8cf825c428c6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624187"
---
|Nome |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do espaço de nome do Autocarro de Serviço](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Os clientes de ônibus de serviço não devem usar uma política de acesso ao nível do espaço de nome que fornece acesso a todas as filas e tópicos em um espaço de nome. Para se alinhar com o modelo de segurança menos privilegiado, deve criar políticas de acesso ao nível da entidade para filas e tópicos para fornecer acesso apenas à entidade específica |Auditoria, Negação, Deficientes |1.0.1 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json)
|[Os registos de diagnóstico no Ônibus de Serviço devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditoriaIfNotExists, Deficiente |2.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json)
