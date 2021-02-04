---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b448fbb9a7d382a785fd66c0edb197499c8c5c79
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99561466"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As fábricas de dados Azure devem ser encriptadas com uma chave gerida pelo cliente (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |Utilize chaves geridas pelo cliente (CMKs) para gerir a encriptação no resto da sua Fábrica de Dados Azure. Por padrão, os dados do cliente são encriptados com chaves geridas pelo serviço, mas os CMKs são geralmente obrigados a cumprir as normas de conformidade regulamentares. Os CMKs permitem que os dados sejam encriptados com uma chave Azure Key Vault criada e propriedade de si. Tem total controlo e responsabilidade pelo ciclo de vida chave, incluindo rotação e gestão. Saiba mais sobre a encriptação CMK em [https://aka.ms/adf-cmk](https://aka.ms/adf-cmk) . |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
|[Acesso público à rede na Azure Data Factory deve ser desativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cf164be-6819-4a50-b8fa-4bcaa4f98fb6) |A desativação da propriedade de acesso à rede pública melhora a segurança, garantindo que a sua Azure Data Factory só pode ser acedida a partir de um ponto final privado. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PublicNetworkAccess_Audit.json) |
