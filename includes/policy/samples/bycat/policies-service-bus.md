---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 76c64c27b08a876ddc0b2b3dbf153248f57f9c9e
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85313663"
---
|Nome<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do espaço de nomes do Service Bus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Os clientes do Service Bus não devem utilizar uma política de acesso ao nível do namespace que proporcione acesso a todas as filas e tópicos num espaço de nome. Para alinhar com o modelo de segurança de menor privilégio, deve criar políticas de acesso ao nível da entidade para filas e tópicos para fornecer acesso apenas à entidade específica |Auditoria, Negar, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Os registos de diagnóstico no Service Bus devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
