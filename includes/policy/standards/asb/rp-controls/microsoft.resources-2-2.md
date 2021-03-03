---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90f4131a616d0cac93ac2a08999178ac7ea8a0eb
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "101728547"
---
|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O fornecimento automático do agente Log Analytics deve ser ativado na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorizar vulnerabilidades e ameaças de segurança, o Azure Security Center recolhe dados das suas máquinas virtuais Azure. Os dados são recolhidos pelo agente Log Analytics, anteriormente conhecido como O Agente de Monitorização da Microsoft (MMA), que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho Log Analytics para análise. Recomendamos que o fornecimento automático de automóveis implemente automaticamente o agente em todos os VMs Azure suportados e quaisquer novos que sejam criados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[O perfil de registo do Azure Monitor deve recolher registos para categorias de 'escrever', 'excluir' e 'acção'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Esta política garante que um perfil de registo recolhe registos para categorias de "escrever", "excluir" e "ação" |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[O Azure Monitor deve recolher registos de atividade de todas as regiões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Esta política audita o perfil de registo do Azure Monitor que não exporta atividades de todas as regiões apoiadas pelo Azure, incluindo a nível global. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
