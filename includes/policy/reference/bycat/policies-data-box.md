---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bd25b94507dd6f124bb03bfb0fccc30adfab90e0
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091022"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os trabalhos da Azure Data Box devem permitir a dupla encriptação para os dados em repouso no dispositivo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Ativar uma segunda camada de encriptação baseada em software para obter dados em repouso no dispositivo. O dispositivo já está protegido através da encriptação Avançada Standard 256-bit encriptação para dados em repouso. Esta opção adiciona uma segunda camada de encriptação de dados. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Os trabalhos da Azure Data Box devem usar uma chave gerida pelo cliente para encriptar a senha de desbloqueio do dispositivo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Utilize uma chave gerida pelo cliente para controlar a encriptação do dispositivo desbloqueie a palavra-passe para Azure Data Box. As chaves geridas pelo cliente também ajudam a gerir o acesso ao dispositivo desbloqueando a palavra-passe através do serviço Data Box, de forma a preparar o dispositivo e copiar dados de forma automatizada. Os dados do próprio dispositivo já estão encriptados em repouso com encriptação Padrão de Encriptação Avançada de 256 bits, e a palavra-passe de desbloqueio do dispositivo é encriptada por padrão com uma chave gerida pela Microsoft. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
