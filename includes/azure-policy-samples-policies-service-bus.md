---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/26/2020
ms.author: dacoulte
ms.openlocfilehash: c534bac1cf8426be2d859c850e41623e7e14f7c3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77780719"
---
|Nome |Descrição |Efeitos(s) |Versão |Origem |
|---|---|---|---|---|
|[Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do espaço de nome do Autocarro de Serviço](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Os clientes do Service Bus não devem utilizar uma política de acesso de nível de espaço de nomes que fornece acesso a todas as filas e tópicos num espaço de nomes. Para se alinhar com o modelo de segurança menos privilegiado, deve criar políticas de acesso ao nível da entidade para filas e tópicos para fornecer acesso apenas à entidade específica |Auditoria, Negação, Deficientes |1.0.1 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json)
|[Os registos de diagnóstico no Ônibus de Serviço devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditoriaIfNotExists, Deficiente |2.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json)
