---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169925"
---
|Nome |Descrição |Efeitos(s) |Versão |
|---|---|---|---|
|[Localizações permitidas](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |Esta política permite-lhe restringir as localizações que a sua organização pode especificar ao implementar recursos. Utilize para impor os requisitos de conformidade geográfica. Exclui grupos de recursos, Microsoft.AzureActiveDirectory/b2cDirectories, e recursos que utilizam a região 'global'. |negar |1.0.0 |
|[Locais permitidos para grupos de recursos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Esta política permite-lhe restringir os locais em que a sua organização pode criar grupos de recursos. Utilize para impor os requisitos de conformidade geográfica. |negar |1.0.0 |
|[Tipos de recursos permitidos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |Esta política permite especificar os tipos de recursos que a sua organização pode implementar. Só os tipos de recursos que suportam as 'tags' e a "localização" serão afetados por esta política. Para restringir todos os recursos, por favor, duplique esta política e mude o "modo" para 'All'. |negar |1.0.0 |
|[A localização do recurso de auditoria corresponde à localização do grupo de recursos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Auditoria que a localização do recurso corresponde à sua localização do grupo de recursos |auditoria |1.0.0 |
|[Utilização de auditoria de regras rbac personalizadas](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Audite funções incorporadas como 'Owner, Contributer, Reader' em vez de funções RBAC personalizadas, que são propensas a erros. A utilização de funções personalizadas é tratada como uma exceção e requer uma revisão rigorosa e modelação de ameaças |Auditoria, Deficientes |1.0.0 |
|[Funções personalizadas do proprietário da subscrição não devem existir](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Esta política garante que não existem funções personalizadas de proprietário de subscrição. |Auditoria, Deficientes |1.0.0 |
|[Tipos de recursos não permitidos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |Esta política permite especificar os tipos de recursos que a sua organização não pode implementar. |Negar |1.0.0 |
