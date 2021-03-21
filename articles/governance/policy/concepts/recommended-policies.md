---
title: Políticas recomendadas para serviços Azure
description: Descreve como encontrar e aplicar políticas recomendadas para serviços Azure, como máquinas virtuais Azure.
ms.date: 09/02/2020
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: 019541f034ce8f0c3728c38d2ae4425308b4e2a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350284"
---
# <a name="recommended-policies-for-azure-services"></a>Políticas recomendadas para serviços Azure

Os clientes que são novos na Política Azure procuram muitas vezes encontrar definições de política comuns para gerir e governar os seus recursos. **As políticas recomendadas** pela Azure Policy fornecem uma lista focalizada de definições de políticas comuns para começar. A experiência **de políticas recomendadas** para recursos apoiados está incorporada na experiência do portal para esse recurso.

Para obter mais incorporados em Azure Policy, consulte [as definições de Azure Policy incorporadas.](../samples/built-in-policies.md)

## <a name="azure-virtual-machines"></a>Máquinas Virtuais do Microsoft Azure

As **políticas recomendadas** para [máquinas virtuais Azure](../../../virtual-machines/index.yml) estão na página **de visão geral** para máquinas virtuais e no separador **Capabilities.** No cartão _Azure Policy,_ selecione o texto "Não configurado" ou "#designado" para abrir um painel lateral com as políticas recomendadas. Qualquer definição de política já atribuída a um âmbito de aplicação da máquina virtual é um membro de is grayed-out. Selecione as políticas recomendadas para aplicar a esta máquina virtual e selecione **Políticas de atribuir** para criar uma atribuição para cada um.

À medida que uma organização atinge a maturidade com [a organização dos seus recursos e hierarquia de recursos,](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions)é recomendado fazer a transição destas atribuições políticas de um por recurso para o nível de grupo de subscrição ou [gestão.](../../management-groups/index.yml)

### <a name="azure-virtual-machines-recommended-policies"></a>Azure Virtual Machines recomenda políticas

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas virtuais sem recuperação de desastres configurada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Audite máquinas virtuais que não tenham a recuperação de desastres configurada. Para saber mais sobre a recuperação de desastres, [https://aka.ms/asr-doc](../../../site-recovery/index.yml) visite. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[VMs de auditoria que não usam discos geridos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Esta política audita VMs que não usam discos geridos |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[A azure Backup deve ser ativado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Esta política ajuda a auditar se o serviço de Backup Azure estiver ativado para todas as máquinas Virtuais. O Azure Backup é uma solução de backup de um clique eficaz em termos de custos, simplifica a recuperação de dados e é mais fácil de permitir do que outros serviços de backup na nuvem. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Veja [Compreender os efeitos do Policy](./effects.md).
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)