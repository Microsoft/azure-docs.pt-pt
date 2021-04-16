---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a9295290e8b0a26c407295c3b8f1ccb025159eac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504294"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas da Cache HPC devem usar a chave gerida pelo cliente para encriptação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Gerencie a encriptação no resto da Cache Azure HPC com chaves geridas pelo cliente. Por padrão, os dados do cliente são encriptados com chaves geridas pelo serviço, mas as chaves geridas pelo cliente são geralmente necessárias para cumprir as normas de conformidade regulamentares. As chaves geridas pelo cliente permitem que os dados sejam encriptados com uma chave Azure Key Vault criada e propriedade de si. Tem total controlo e responsabilidade pelo ciclo de vida chave, incluindo rotação e gestão. |Auditoria, Deficientes, Negar |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
