---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5f89c1236b1c794bb49e4eff667cb77b8d1ea08e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605423"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O ponto final do Serviço Bot deve ser um HTTPS URI válido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Os dados podem ser adulterados durante a transmissão. Existem protocolos que disponibilizam encriptação para resolver problemas de utilização indevida e adulteração. Para garantir que os seus bots estão a comunicar apenas através de canais encriptados, desaponte o ponto final para um HTTPS URI válido. Isto garante que o protocolo HTTPS é utilizado para encriptar os seus dados em trânsito e é também frequentemente um requisito para o cumprimento das normas regulamentares ou do setor. Visite: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |auditoria, negação, desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[O Serviço bot deve ser encriptado com uma chave gerida pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |O Azure Bot Service encripta automaticamente o seu recurso para proteger os seus dados e cumprir os compromissos de segurança organizacional e de conformidade. Por predefinição, são utilizadas teclas de encriptação geridas pela Microsoft. Para uma maior flexibilidade na gestão de chaves ou no controlo do acesso à sua subscrição, selecione as teclas geridas pelo cliente, também conhecidas como trazer a sua própria chave (BYOK). Saiba mais sobre a encriptação do Serviço Azure Bot: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |auditoria, negação, desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
