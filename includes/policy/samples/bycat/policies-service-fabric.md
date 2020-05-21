---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 22f6f040d333a526940a688c16277be568520f50
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651784"
---
|Name |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para encriptar AndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |O Tecido de Serviço fornece três níveis de proteção (Nenhum, Sinal e Encriptação) para comunicação nó-a-nó utilizando um certificado de cluster primário. Desestabeleça o nível de proteção para garantir que todas as mensagens de nós-a-nó são encriptadas e assinadas digitalmente |Auditoria, Negação, Deficientes |1.1.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Utilização auditada da autenticação do cliente apenas via Diretório Ativo Azure em Tecido de Serviço |Auditoria, Negação, Deficientes |1.1.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
