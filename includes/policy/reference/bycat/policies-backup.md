---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/16/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ddb6402e4250ca9b361bcb40a393c125d271fdf6
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982212"
---
|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A azure Backup deve ser ativado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Garantir a proteção das suas Máquinas Virtuais Azure, ativando a cópia de segurança do Azure. O Azure Backup é uma solução segura e eficaz de proteção de dados para o Azure. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Configurar backup em VMs de um local para um cofre central existente no mesmo local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Esta política configura a proteção de backup do Azure em VMs num dado local para um cofre central existente no mesmo local. Aplica-se apenas aos VM que ainda não estão configurados para cópia de segurança. Recomenda-se que esta política não seja atribuída a mais de 200 VMs. Se a apólice for atribuída a mais de 200 VMs, pode resultar no disparo de backup algumas horas para além do calendário definido. Esta política será reforçada para suportar mais imagens VM. |deployIfNotExists, auditIfNotExists, desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Implementar Definições de diagnóstico para cofre de serviços de recuperação para registar espaço de trabalho para categorias específicas de recursos.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Implementar Definições de diagnóstico para o cofre de serviços de recuperação para transmitir para log analytics espaço de trabalho para categorias específicas de recursos. Se alguma das categorias específicas do recurso não estiver ativada, é criada uma nova definição de diagnóstico. |implementarIfNotExists |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
