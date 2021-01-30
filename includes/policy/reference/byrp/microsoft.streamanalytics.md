---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6e103ff01b09eb84610472d6ab2f6f113f7a981d
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096474"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os trabalhos do Azure Stream Analytics devem usar chaves geridas pelo cliente para encriptar dados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |Utilize chaves geridas pelo cliente quando pretender armazenar de forma segura quaisquer metadados e ativos de dados privados dos seus trabalhos stream Analytics na sua conta de armazenamento. Isto dá-lhe controlo total sobre a forma como os seus dados stream Analytics são encriptados. |auditoria, negação, desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |
|[Implementar definições de diagnóstico para stream analytics para o centro de eventos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedf3780c-3d70-40fe-b17e-ab72013dafca) |Implementa as definições de diagnóstico para stream Analytics para transmitir para um Centro de Eventos regional quando qualquer Stream Analytics que esteja faltando esta definição de diagnóstico é criado ou atualizado. |ImplementarIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Implementar definições de diagnóstico para stream analytics para log analytics espaço de trabalho](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237e0f7e-b0e8-4ec4-ad46-8c12cb66d673) |Implementa as definições de diagnóstico para stream Analytics para transmitir para um espaço de trabalho regional do Log Analytics quando qualquer Stream Analytics que esteja faltando esta definição de diagnóstico é criado ou atualizado. |ImplementarIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Os registos de diagnóstico no Azure Stream Analytics devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
