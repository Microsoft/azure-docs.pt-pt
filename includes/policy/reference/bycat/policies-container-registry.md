---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b4b02ffddb8f691e395b9b5baf6780cc9769e69d
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487888"
---
|Nome<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de contentores devem ser encriptados com uma chave gerida pelo cliente (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Registos de contentores de auditoria que não tenham encriptação ativada com chaves geridas pelo cliente (CMK). O Azure encripta automaticamente o conteúdo do registo em repouso com as teclas geridas pelo serviço. Pode complementar a encriptação padrão com uma camada de encriptação adicional utilizando uma chave que cria e gere no Cofre da Chave Azure. Para mais informações sobre encriptação CMK, visite: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Os registos de contentores não devem permitir o acesso ilimitado à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Auditar registos de contentores que não tenham nenhuma regra de rede ou firewall (IP) configuradas e assim permitir todo o acesso à rede por padrão. Restringir o acesso à rede protege os registos de contentores de potenciais ameaças. Os registos de contentores com pelo menos uma regra IP/firewall ou uma rede virtual configurada são considerados conformes. Para obter mais informações sobre as regras da rede de registo de contentores, visite: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) e [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Os registos de contentores devem usar links privados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Registos de contentores de auditoria que não tenham pelo menos uma ligação de ponto final privado aprovado. Os clientes de uma rede virtual podem aceder de forma segura a recursos que possuem ligações privadas de ponto final através de links privados. O acesso público pode então ser desativado para garantir que apenas os links privados podem ser utilizados para se ligarem ao registo. Para mais informações, visite: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
