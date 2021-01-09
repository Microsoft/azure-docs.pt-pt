---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0688e5e0703f74ee48e37119edcece465e51b5e5
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98043889"
---
|Nome<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Configuração da Aplicação deve usar uma chave gerida pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |As teclas geridas pelo cliente fornecem uma proteção de dados melhorada, permitindo-lhe gerir as suas chaves de encriptação. Isto é frequentemente necessário para satisfazer os requisitos de conformidade. |Auditoria, Negar, Deficientes |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[A Configuração da Aplicação deve usar link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |O Azure Private Link permite-lhe ligar a sua rede virtual aos serviços Azure sem um endereço IP público na fonte ou destino. A plataforma de ligação privada trata da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. Ao mapear pontos finais privados para as suas instâncias de configuração da aplicação em vez de todo o serviço, também estará protegido contra riscos de fuga de dados. Saiba mais em: [https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint) . |AuditIfNotExists, Desativado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
