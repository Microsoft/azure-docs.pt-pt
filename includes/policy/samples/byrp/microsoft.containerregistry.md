---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 37c3899ecacb445258c58252d3fea545f9ea380f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658279"
---
|Name |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Os registos de contentores devem ser encriptados com uma chave gerida pelo cliente (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Registos de contentores de auditoria que não tenham encriptação ativada com chaves geridas pelo cliente (CMK). Para mais informações sobre encriptação cmk, visite: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Auditoria, Deficientes |1.0.0-pré-visualização |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Os registos de contentores não devem permitir o acesso ilimitado à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Registos de contentores de auditoria que não tenham nenhuma Rede (IP ou VNET) Regras configuradas e permitam o acesso de toda a rede por padrão. Os Registos de Contentores com pelo menos uma regra IP/Firewall ou rede virtual configurada serão considerados conformes. Para mais informações sobre as regras da Rede de Registo de Contentores, visite: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Auditoria, Deficientes |1.0.0-pré-visualização |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Os registos de contentores devem utilizar ligações privadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Registos de contentores de auditoria que não possuam pelo menos uma ligação de ponto final privado aprovada. Os clientes de uma rede virtual podem aceder de forma segura a recursos que têm ligações de ponto final privado através de ligações privadas. Para mais informações, visite: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Auditoria, Deficientes |1.0.0-pré-visualização |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[O Registo de Contentores deve utilizar um ponto final do serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita qualquer Registo de Contentores não configurado para utilizar um ponto final do serviço de rede virtual. |Auditoria, Deficientes |1.0.0-pré-visualização |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
