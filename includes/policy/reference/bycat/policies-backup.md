---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4c895a796a23fb62bb5f65e7a82a96916be9c438
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487909"
---
|Nome<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A azure Backup deve ser ativado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Esta política ajuda a auditar se o serviço de Backup Azure estiver ativado para todas as máquinas Virtuais. O Azure Backup é uma solução de backup de um clique eficaz em termos de custos, simplifica a recuperação de dados e é mais fácil de permitir do que outros serviços de backup na nuvem. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Configurar backup em VMs de um local para um cofre central existente no mesmo local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Esta política configura a proteção de backup do Azure em VMs num dado local para um cofre central existente no mesmo local. Aplica-se apenas aos VM que ainda não estão configurados para cópia de segurança. Recomenda-se que esta política não seja atribuída a mais de 200 VMs. Se a apólice for atribuída a mais de 200 VMs, pode resultar no disparo de backup algumas horas para além do calendário definido. Esta política será reforçada para suportar mais imagens VM. |deployIfNotExists, auditIfNotExists, desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
