---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 553627e0ae32aff698ac2d38f8eb22e0191739ac
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611104"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de contentores devem ser encriptados com uma chave gerida pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Utilize as chaves geridas pelo cliente para gerir a encriptação no resto do conteúdo dos seus registos. Por padrão, os dados são encriptados em repouso com chaves geridas pelo serviço, mas as chaves geridas pelo cliente são geralmente necessárias para cumprir as normas de conformidade regulamentar. As chaves geridas pelo cliente permitem que os dados sejam encriptados com uma chave Azure Key Vault criada e propriedade de si. Tem total controlo e responsabilidade pelo ciclo de vida chave, incluindo rotação e gestão. Saiba mais em [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Auditoria, Negar, Deficientes |[1.1.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Os registos de contentores não devem permitir o acesso ilimitado à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Os registos de contentores Azure por padrão aceitam ligações através da internet de anfitriões em qualquer rede. Para proteger os seus registos de ameaças potenciais, permita o acesso a partir de endereços IP públicos específicos ou intervalos de endereços. Se o seu registo não tiver uma regra IP/firewall ou uma rede virtual configurada, aparecerá nos recursos pouco saudáveis. Saiba mais sobre as regras da rede de registo de contentores aqui: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) e aqui [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Os registos de contentores devem usar ligação privada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |O Azure Private Link permite-lhe ligar a sua rede virtual aos serviços Azure sem um endereço IP público na fonte ou destino. A plataforma de ligação privada trata da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. Ao mapear pontos finais privados para os seus registos de contentores em vez de todo o serviço, também estará protegido contra riscos de fuga de dados. Saiba mais em: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
