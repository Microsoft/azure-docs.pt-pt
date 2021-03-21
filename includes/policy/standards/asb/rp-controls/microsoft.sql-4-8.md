---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b7ca6a2c038a7ccf6f74d0f86adb68f4204942a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586913"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As instâncias geridas pela SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Implementar a Encriptação de Dados Transparentes (TDE) com a sua própria chave proporciona-lhe uma maior transparência e controlo sobre o Protetor TDE, maior segurança com um serviço externo apoiado pelo HSM e promoção da separação de direitos. Esta recomendação aplica-se a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, Desativado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Os servidores SQL devem usar as chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementar a Encriptação de Dados Transparentes (TDE) com a sua própria chave proporciona uma maior transparência e controlo sobre o Protetor TDE, uma maior segurança com um serviço externo apoiado pelo HSM e a promoção da separação de direitos. Esta recomendação aplica-se a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, Desativado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[A encriptação transparente de dados nas bases de dados SQL deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A encriptação transparente de dados deve ser ativada para proteger os dados em repouso e cumprir os requisitos de conformidade |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
