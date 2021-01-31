---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1a70bbf434084faca5c8648f8901ec77675ca5fe
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220467"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As fábricas de dados Azure devem ser encriptadas com uma chave gerida pelo cliente (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |Utilize chaves geridas pelo cliente (CMKs) para gerir a encriptação no resto da sua Fábrica de Dados Azure. Por padrão, os dados do cliente são encriptados com chaves geridas pelo serviço, mas os CMKs são geralmente obrigados a cumprir as normas de conformidade regulamentares. Os CMKs permitem que os dados sejam encriptados com uma chave Azure Key Vault criada e propriedade de si. Tem total controlo e responsabilidade pelo ciclo de vida chave, incluindo rotação e gestão. Saiba mais sobre a encriptação CMK em [https://aka.ms/adf-cmk](https://aka.ms/adf-cmk) . |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
