---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f10f0d5cbd76597dd08c0beb172a51195d83d266
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235652"
---
|Name |Descrição |Efeito(s) |Versão |GitHub |
|---|---|---|---|---|
|[Os registos de contentores devem ser encriptados com uma chave gerida pelo cliente (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Registos de contentores de auditoria que não têm encriptação ativada com Chaves Geridas pelo Cliente (CMK). Para mais informações sobre encriptação CMK, visite: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Auditoria, Deficientes |1.0.0-pré-visualização |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Os registos de contentores não devem permitir o acesso ilimitado à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Registos de contentores de auditoria que não tenham nenhuma Rede (IP ou VNET) Regras configuradas e permitem todo o acesso à rede por predefinição. Os registos de contentores com pelo menos uma regra IP/Firewall ou uma rede virtual configurada serão considerados conformes. Para mais informações sobre as regras da Rede de Registo de Contentores, visite: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Auditoria, Deficientes |1.0.0-pré-visualização |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Os registos de contentores devem utilizar links privados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Registos de contentores de auditoria que não tenham pelo menos uma ligação de ponto final privado aprovado. Os clientes de uma rede virtual podem aceder de forma segura a recursos que possuem ligações privadas de ponto final através de links privados. Para mais informações, visite: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Auditoria, Deficientes |1.0.0-pré-visualização |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[O Registo de Contentores deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita qualquer registo de contentores não configurado para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |1.0.0-pré-visualização |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
