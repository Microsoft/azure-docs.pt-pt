---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7a5717f7bcb891f78054e3093c89f75622494aff
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837805"
---
|Name |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Backup Azure deve ser ativado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Esta política ajuda a auditar se o serviço de backup Azure estiver ativado para todas as máquinas Virtuais. A Backup Azure é uma solução de backup de um clique e rentável, simplifica a recuperação de dados e é mais fácil de permitir do que outros serviços de backup em nuvem. |AuditoriaIfNotExists, Deficiente |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Configure cópias de segurança em VMs de um local para um cofre central existente no mesmo local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Esta política confunde a proteção de backup azure em VMs em um dado local para um cofre central existente no mesmo local. Aplica-se apenas aos VMs que ainda não estão configurados para backup. Recomenda-se que esta política seja atribuída a não mais de 200 VMs. Se a política for atribuída para mais de 200 VMs, pode resultar no disparo do backup algumas horas para além do calendário definido. Esta política será reforçada para apoiar mais imagens VM. |implementifNotexists, auditIfNotExists, desativado |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Implemente as Definições de Diagnóstico para serviços de recuperação Vault para log Analytics espaço de trabalho para categorias específicas de recursos.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Implemente as Definições de Diagnóstico para o Cofre de Serviços de Recuperação para transmitir para log Analytics espaço de trabalho para categorias específicas de Recursos. Se alguma das categorias específicas do Recurso não estiver ativada, é criada uma nova definição de diagnóstico. |implementarIfNotExists |1.0.0-pré-visualização |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
