---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 7100231e1cc4590d61724668f3941591c6b00206
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758420"
---
|Nome |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Apenas ligações seguras ao seu Redis Cache devem ser ativadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria que permite apenas ligações via SSL a Redis Cache. A utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, escutas e sequestro de sessão |Auditoria, Negação, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
