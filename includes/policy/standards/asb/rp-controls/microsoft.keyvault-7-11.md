---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a6d26eb0fb795f0ca047ff4a976a5b31b9493ee
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512117"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os cofres-chave devem ter proteção de purga ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A eliminação maliciosa de um cofre chave pode levar à perda permanente de dados. Um infiltrado malicioso na sua organização pode potencialmente apagar e purgar cofres de chaves. A proteção de purga protege-o de ataques de infiltrados, impondo um período de retenção obrigatório para cofres-chave apagados suaves. Ninguém dentro da sua organização ou microsoft será capaz de limpar os seus cofres chave durante o período de retenção de eliminação suave. |Auditoria, Negar, Deficientes |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
