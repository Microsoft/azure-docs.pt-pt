---
title: Definições políticas incorporadas para norma de proteção DDoS Azure DDoS
description: Lista definições de políticas incorporadas da Azure DDoS Policy para a Norma de Proteção DDoS Azure. Estas definições políticas incorporadas fornecem abordagens comuns para gerir os seus recursos Azure.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: b58dddef0aa63aec525e039f333a7a785c32d23e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875652"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Definições de Azure Policy incorporadas para Azure DDoS Protection Standard

Esta página é um índice de definições de política incorporadas da [Azure Policy](../governance/policy/overview.md) para Azure DDoS Protection Standard. Para obter mais incorporados em Azure Policy para outros serviços, consulte [definições incorporadas da Política Azure](../governance/policy/samples/built-in-policies.md).

O nome de cada definição de política incorporada liga-se à definição de política no portal Azure. Utilize o link na coluna **versão** para visualizar a fonte no [repo GitHub da Política Azure](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Norma do Azure DDoS Protection

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As redes virtuais devem ser protegidas pela Norma de Proteção DDoS da Azure DDoS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Proteja as suas redes virtuais contra ataques volumosos e protocolares com o Azure DDoS Protection Standard. Para mais informações, [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs) visite.|Modificar, Auditar, Desativar|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Os endereços IP públicos devem ter registos de recursos habilitados para a Norma de Proteção DDoS do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Permitir registos de recursos para endereços IP públicos em definições de diagnóstico para transmitir para um espaço de trabalho Log Analytics. Obtenha uma visibilidade detalhada no tráfego de ataque e as ações tomadas para mitigar os ataques de DDoS através de notificações, relatórios e registos de fluxo.|AuditIfNotExists, DeployIfNotExists, Desativados|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Passos seguintes

- Veja as incorporações no [repositório do GitHub do Azure Policy](https://github.com/Azure/azure-policy).
- Reveja a [estrutura de definição do Azure Policy](../governance/policy/concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../governance/policy/concepts/effects.md).
