---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 289a2fe7fecc3e28d646e98692c1d5ada4ea7fa8
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097243"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os servidores SQL com auditoria ao destino da conta de armazenamento devem ser configurados com retenção de 90 dias ou superior](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Para efeitos de investigação de incidentes, recomendamos definir a retenção de dados para a auditoria do seu SQL Server para o destino da conta de armazenamento para pelo menos 90 dias. Confirme que está a cumprir as regras de retenção necessárias para as regiões em que está a operar. Isto é, por vezes, necessário para o cumprimento das normas regulamentares. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
