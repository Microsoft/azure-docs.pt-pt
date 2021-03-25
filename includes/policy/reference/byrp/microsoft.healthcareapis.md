---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1112aa3662ad6112049b02fa0109b3929cf509b1
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035149"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Azure API para FHIR deve usar uma chave gerida pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Utilize uma chave gerida pelo cliente para controlar a encriptação no resto dos dados armazenados na Azure API para fHIR quando este é um requisito regulamentar ou de conformidade. As teclas geridas pelo cliente também fornecem encriptação dupla adicionando uma segunda camada de encriptação em cima da padrão feita com teclas geridas pelo serviço. |auditoria, deficiente |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[A Azure API para FHIR deve usar link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |A Azure API para fHIR deve ter pelo menos uma ligação de ponto final privado aprovada. Os clientes de uma rede virtual podem aceder de forma segura a recursos que possuem ligações privadas de ponto final através de links privados. Para mais informações, visite: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink) . |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[O CORS não deve permitir que todos os domínios acedam à sua API para FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |A Partilha de Recursos de Origem Cruzada (CORS) não deve permitir que todos os domínios acedam à sua API para FHIR. Para proteger a sua API para FHIR, remova o acesso a todos os domínios e defina explicitamente os domínios autorizados a ligar. |auditoria, deficiente |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
