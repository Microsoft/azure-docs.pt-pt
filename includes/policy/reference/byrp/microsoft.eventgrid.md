---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 45c31b519ed2e9db26a29743309744145c5bc6e4
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703538"
---
|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os domínios da grelha de eventos Azure devem usar link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |O Azure Private Link permite-lhe ligar a sua rede virtual aos serviços Azure sem um endereço IP público na fonte ou destino. A plataforma de ligação privada trata da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. Ao mapear pontos finais privados para os seus domínios de Grade de Eventos em vez de todo o serviço, também estará protegido contra riscos de fuga de dados. Saiba mais em: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Os tópicos da Grelha de Eventos Azure devem usar link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |O Azure Private Link permite-lhe ligar a sua rede virtual aos serviços Azure sem um endereço IP público na fonte ou destino. A plataforma de ligação privada trata da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. Ao mapear pontos finais privados para os seus tópicos em vez de todo o serviço, também estará protegido contra riscos de fuga de dados. Saiba mais em: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
