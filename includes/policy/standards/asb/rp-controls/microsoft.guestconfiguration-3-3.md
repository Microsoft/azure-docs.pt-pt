---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c495e2f25f9e55fc3147ca4c8bcddf51288865ea
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021650"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Máquinas de auditoria do Windows em falta de qualquer um dos membros especificados no grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o grupo de administradores locais não contiver um ou mais membros que estão listados no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Auditar máquinas Windows que têm contas extra no grupo de Administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o grupo de administradores locais contiver membros que não estão listados no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[Auditar máquinas Windows que têm os membros especificados no grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o grupo de administradores locais contiver um ou mais dos membros listados no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
