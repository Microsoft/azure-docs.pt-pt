---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 70f547dc5d035b57f636719c5500ba811fc69a1a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "96007943"
---
|Nome<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |O Tecido de Serviço fornece três níveis de proteção (Nenhum, Sinal e DesignAndSign) para comunicação nó-a-nó usando um certificado de cluster primário. Descreva o nível de proteção para garantir que todas as mensagens nó-a-nó são encriptadas e assinadas digitalmente |Auditoria, Negar, Deficientes |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Os clusters de tecido de serviço só devem utilizar o Azure Ative Directy para a autenticação do cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Utilização de auditoria da autenticação do cliente apenas através do Azure Ative Directory in Service Fabric |Auditoria, Negar, Deficientes |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
