---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/26/2020
ms.author: dacoulte
ms.openlocfilehash: a9a65b967f7c6d16497c9f6f0fb53ec290a824a2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77780488"
---
|Nome |Descrição |Efeitos(s) |Versão |Origem |
|---|---|---|---|---|
|[Os registos de contentores devem ser encriptados com uma chave gerida pelo cliente (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Registos de contentores de auditoria que não tenham encriptação ativada com chaves geridas pelo cliente (CMK). Para mais informações sobre encriptação cmk, visite: https://aka.ms/acr/CMK. |Auditoria, Deficientes |1.0.0-pré-visualização |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Os registos de contentores não devem permitir o acesso ilimitado à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Registos de contentores de auditoria que não tenham nenhuma Rede (IP ou VNET) Regras configuradas e permitam o acesso de toda a rede por padrão. Os Registos de Contentores com pelo menos uma regra IP/Firewall ou rede virtual configurada serão considerados conformes. Para mais informações sobre as regras da Rede de Registo de Contentores, visite: https://aka.ms/acr/vnet. |Auditoria, Deficientes |1.0.0-pré-visualização |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
