---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f3520d90cdcbee77cf8c4989e5575dc0e15601ab
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83637342"
---
|Name |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Implementar definições de diagnóstico para a conta de lote para o centro de eventos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdb51110f-0865-4a6e-b274-e2e07a5b2cd7) |Implementa as definições de diagnóstico da Conta de Lote para transmitir para um Centro regional de Eventos quando qualquer Conta de Lote que esteja em falta nesta definição de diagnóstico é criada ou atualizada. |ImplementaçãoIfNotExists, Desativado |2.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Implementar definições de diagnóstico para conta de lote para log analytics espaço de trabalho](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc84e5349-db6d-4769-805e-e14037dab9b5) |Implementa as definições de diagnóstico da Conta de Lote para transmitir para um espaço de trabalho regional log Analytics quando qualquer conta de lote que esteja em falta nesta definição de diagnóstico é criada ou atualizada. |ImplementaçãoIfNotExists, Desativado |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Os registos de diagnóstico nas contas do Lote devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditoriaIfNotExists, Deficiente |2.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[As regras de alerta métrico devem ser configuradas nas contas do Lote](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Configuração de auditoria das regras de alerta métrico na conta do Lote para ativar a métrica necessária |AuditoriaIfNotExists, Deficiente |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |
