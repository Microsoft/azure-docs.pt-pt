---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 0447d2e3a5a8f8d39ece7b44522d4a14227360f1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80623973"
---
|Nome |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Backup Azure deve ser ativado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Esta política ajuda a auditar se o serviço de backup Azure estiver ativado para todas as máquinas Virtuais. A Backup Azure é uma solução de backup de um clique e rentável, simplifica a recuperação de dados e é mais fácil de permitir do que outros serviços de backup em nuvem. |AuditoriaIfNotExists, Deficiente |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Configure cópias de segurança em VMs de um local para um cofre central existente no mesmo local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Esta política confunde a proteção de backup azure em VMs em um dado local para um cofre central existente no mesmo local. Aplica-se apenas aos VMs que ainda não estão configurados para backup. Recomenda-se que esta política seja atribuída a não mais de 200 VMs. Se a política for atribuída para mais de 200 VMs, pode resultar no disparo do backup algumas horas para além do calendário definido. Esta política será reforçada para apoiar mais imagens VM. |implementifNotexists, auditIfNotExists, desativado |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
