---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 77b011300d7673d4d96d89fda3f19dacf9c635e9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758321"
---
|Nome |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para encriptar AndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |O Tecido de Serviço fornece três níveis de proteção (Nenhum, Sinal e Encriptação) para comunicação nó-a-nó utilizando um certificado de cluster primário. Desestabeleça o nível de proteção para garantir que todas as mensagens de nós-a-nó são encriptadas e assinadas digitalmente |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json)
|[Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Utilização auditada da autenticação do cliente apenas via Diretório Ativo Azure em Tecido de Serviço |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json)
