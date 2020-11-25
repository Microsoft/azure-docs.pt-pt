---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3557287cff5cb3f06f52eb99d1498af5e2355651
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "96007813"
---
|Nome<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache para Redis deve residir dentro de uma rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |O Azure Cache para Redis tem a capacidade de residir dentro de uma rede virtual, que é uma forma de o recurso ter um ponto final não público controlado e gerido pelo utilizador. |Auditoria, Negar, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria que permite apenas ligações via SSL a Azure Cache para Redis. A utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
