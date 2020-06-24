---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7baa1c030c8d70b76eb661d6a6fac7429a80a50e
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84709912"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A azure Backup deve ser ativado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Esta política ajuda a auditar se o serviço de Backup Azure estiver ativado para todas as máquinas Virtuais. O Azure Backup é uma solução de backup de um clique eficaz em termos de custos, simplifica a recuperação de dados e é mais fácil de permitir do que outros serviços de backup na nuvem. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Configurar backup em VMs de um local para um cofre central existente no mesmo local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Esta política configura a proteção de backup do Azure em VMs num dado local para um cofre central existente no mesmo local. Aplica-se apenas aos VM que ainda não estão configurados para cópia de segurança. Recomenda-se que esta política não seja atribuída a mais de 200 VMs. Se a apólice for atribuída a mais de 200 VMs, pode resultar no disparo de backup algumas horas para além do calendário definido. Esta política será reforçada para suportar mais imagens VM. |deployIfNotExists, auditIfNotExists, desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Implementar Definições de diagnóstico para cofre de serviços de recuperação para registar espaço de trabalho para categorias específicas de recursos.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Implementar Definições de diagnóstico para o cofre de serviços de recuperação para transmitir para log analytics espaço de trabalho para categorias específicas de recursos. Se alguma das categorias específicas do recurso não estiver ativada, é criada uma nova definição de diagnóstico. |implementarIfNotExists |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
