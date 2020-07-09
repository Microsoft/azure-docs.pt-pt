---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d3d4ce0f27e551d628161aca996dfa53ee0820b1
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85317879"
---
|Nome<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gerir tipos de chave de certificados permitidos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1151cede-290b-4ba0-8b38-0ad145ac888f) |Esta política gere os tipos-chave permitidos para certificados. |auditoria, negação, desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_AllowedKeyTypes.json) |
|[Gerir nomes de curvas permitidos para certificados de criptografia de curva elíptica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd78111f-4953-4367-9fd5-7e08808b54bf) |Esta política gere os nomes de curvas elípticas permitidas para certificados de criptografia de curva elíptica. |auditoria, negação, desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_EC_AllowedCurveNames.json) |
|[Gerir gatilhos de ação vitalícia de certificado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12ef42cb-9903-4e39-9c26-422d29570417) |Esta política gere a configuração para a ação vitalícia do certificado antes da expiração do certificado. |auditoria, negação, desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_LifetimeAction.json) |
|[Gerir o período de validade do certificado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |Esta política gere o período máximo de validade dos certificados em meses. |auditoria, negação, desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[Gerir certificados emitidos por uma AC não integrada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa22f4a40-01d3-4c7d-8071-da157eeff341) |Esta apólice gere os certificados emitidos por uma Autoridade de Certificados não integrada especificada. |auditoria, negação, desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_CustomCAs.json) |
|[Gerir certificados emitidos por uma AC integrada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e826246-c976-48f6-b03e-619bb92b3d82) |Esta apólice gere os certificados emitidos por uma autoridade de certificados integrada de cofre de chaves especificada. |auditoria, negação, desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_SupportedCAs.json) |
|[Gerir certificados que se encontram dentro de um número especificado de dias de expiração](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff772fb64-8e40-40ad-87bc-7706e1949427) |Esta política gere certificados que se encontram dentro de um determinado número de dias até à data de validade. |auditoria, negação, desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Expiry_ByDays.json) |
|[Gerir o tamanho mínimo da chave para certificados RSA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee51871-e572-4576-855c-047c820360f0) |Esta política gere o tamanho mínimo da chave para os certificados RSA. |auditoria, negação, desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_RSA_MinimumKeySize.json) |
