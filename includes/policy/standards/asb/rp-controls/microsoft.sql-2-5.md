---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 564c07c3a59e13dda5fbbc992855d5ca6019dad6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586889"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os servidores SQL devem reter dados de auditoria durante pelo menos 90 dias](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Para efeitos de investigação de incidentes, recomendamos definir a retenção de dados para os dados de auditoria dos seus servidores SQL para pelo menos 90 dias. Confirme que está a cumprir as regras de retenção necessárias para as regiões em que está a operar. Isto é, por vezes, necessário para o cumprimento das normas regulamentares. |AuditIfNotExists, Desativado |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
