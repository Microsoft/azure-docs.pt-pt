---
title: Avaliar o impacto de uma nova política do Azure
description: Entenda o processo a ser seguido ao introduzir uma nova política em seu ambiente do Azure.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 6c1a3ea764aedec7de5eab1f564176eaafead70b
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959833"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>Avaliar o impacto de uma nova política do Azure

Azure Policy é uma ferramenta poderosa para gerenciar seus recursos do Azure para padrões de negócios e atender às necessidades de conformidade. Quando as pessoas, os processos ou os pipelines criam ou atualizam recursos, Azure Policy revisa a solicitação. Quando o efeito de definição de política é [Append](./effects.md#deny) ou [DeployIfNotExists](./effects.md#deployifnotexists), Policy altera a solicitação ou adiciona a ela. Quando o efeito de definição de política é [Audit](./effects.md#audit) ou [AuditIfNotExists](./effects.md#auditifnotexists), Policy faz com que uma entrada do log de atividades seja criada. E quando o efeito de definição de política é [Deny](./effects.md#deny), a política interrompe a criação ou a alteração da solicitação.

Esses resultados são exatamente os desejados quando você sabe que a política está definida corretamente. No entanto, é importante validar que uma nova política funcione como pretendida antes de permitir que ela mude ou bloqueie o trabalho. A validação deve garantir que apenas os recursos pretendidos sejam determinados como sem conformidade e que nenhum recurso compatível seja incluído incorretamente (conhecido como _falso positivo_) nos resultados.

A abordagem recomendada para validar uma nova definição de política é seguir estas etapas:

- Definir rigidamente sua política
- Auditar seus recursos existentes
- Auditar solicitações de recursos novas ou atualizadas
- Implantar sua política para recursos
- Monitorização contínua

## <a name="tightly-define-your-policy"></a>Definir rigidamente sua política

É importante entender como a política de negócios é implementada como uma definição de política e a relação dos recursos do Azure com outros serviços do Azure. Essa etapa é realizada [identificando os requisitos](../tutorials/create-custom-policy-definition.md#identify-requirements) e [determinando as propriedades do recurso](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Mas também é importante ver além da definição estreita de sua política de negócios. O estado da política, por exemplo, "todas as máquinas virtuais devem..."? E quanto a outros serviços do Azure que fazem uso de VMs, como HDInsight ou AKS? Ao definir uma política, devemos considerar como essa política afeta os recursos que são usados por outros serviços.

Por esse motivo, as definições de política devem ser tão definidas e focadas nos recursos e nas propriedades que você precisa avaliar para obter a conformidade o mais possível.

## <a name="audit-existing-resources"></a>Auditar recursos existentes

Antes de procurar gerenciar recursos novos ou atualizados com sua nova definição de política, é melhor ver como ele avalia um subconjunto limitado de recursos existentes, como um grupo de recursos de teste. Use o [modo de imposição](./assignment-structure.md#enforcement-mode)
_desabilitado_ (DoNotEnforce) em sua atribuição de política para impedir que o [efeito](./effects.md) de disparar ou entradas do log de atividades seja criado.

Esta etapa lhe dá a chance de avaliar os resultados de conformidade da nova política nos recursos existentes sem afetar o fluxo de trabalho. Verifique se nenhum recurso compatível está marcado como não compatível (_falso positivo_) e se todos os recursos que você espera que não estejam em conformidade estão marcados corretamente.
Depois que o subconjunto inicial de recursos é validado como esperado, expanda lentamente a avaliação para todos os recursos existentes.

A avaliação de recursos existentes dessa maneira também oferece uma oportunidade de corrigir recursos sem conformidade antes da implementação completa da nova política. Essa limpeza pode ser feita manualmente ou por meio de uma [tarefa de correção](../how-to/remediate-resources.md) se o efeito de definição de política for _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Auditar recursos novos ou atualizados

Depois de validar que sua nova definição de política está se comunicando corretamente nos recursos existentes, é hora de examinar o impacto da política quando os recursos forem criados ou atualizados. Se a definição de política der suporte à parametrização de efeito, use [Audit](./effects.md#audit). Essa configuração permite que você monitore a criação e a atualização de recursos para ver se a nova definição de política dispara uma entrada no log de atividades do Azure para um recurso que não está em conformidade sem afetar o trabalho ou as solicitações existentes.

É recomendável atualizar e criar novos recursos que correspondam à definição de política para ver que o efeito de _auditoria_ está sendo disparado corretamente quando esperado. Esteja atento a solicitações de recursos que não devem ser afetadas pela nova definição de política que disparam o efeito de _auditoria_ .
Esses recursos impactados são outro exemplo de _falsos positivos_ e devem ser corrigidos na definição de política antes da implementação completa.

No caso de a definição de política ser alterada neste estágio de teste, é recomendável iniciar o processo de validação com a auditoria de recursos existentes. Uma alteração na definição de política para um _falso positivo_ sobre recursos novos ou atualizados provavelmente também terá um impacto nos recursos existentes.

## <a name="deploy-your-policy-to-resources"></a>Implantar sua política para recursos

Depois de concluir a validação da nova definição de política com recursos existentes e solicitações de recursos novas ou atualizadas, você inicia o processo de implementação da política. É recomendável criar a atribuição de política para a nova definição de política para um subconjunto de todos os recursos primeiro, como um grupo de recursos. Depois de validar a implantação inicial, estenda o escopo da política para níveis mais amplos e mais amplos, como assinaturas e grupos de gerenciamento. Essa expansão é obtida removendo a atribuição e criando uma nova nos escopos de destino até que ela seja atribuída ao escopo completo de recursos que devem ser cobertos pela nova definição de política.

Durante a distribuição, se estiverem localizados recursos que devem ser isentos da nova definição de política, resolva-os de uma das seguintes maneiras:

- Atualize a definição de política para ser mais explícita para reduzir o impacto indesejado
- Alterar o escopo da atribuição de política (removendo e criando uma nova atribuição)
- Adicionar o grupo de recursos à lista de exclusões para a atribuição de política

Todas as alterações no escopo (nível ou exclusões) devem ser totalmente validadas e comunicadas com suas organizações de segurança e conformidade para garantir que não haja nenhuma lacuna na cobertura.

## <a name="monitor-your-policy-and-compliance"></a>Monitore sua política e conformidade

A implementação e a atribuição da definição de política não é a etapa final. Monitore continuamente o nível de [conformidade](../how-to/get-compliance-data.md) dos recursos para sua nova definição de política e a instalação apropriada [Azure monitor alertas e notificações](../../../azure-monitor/platform/alerts-overview.md) para quando dispositivos não compatíveis forem identificados. Também é recomendável avaliar a definição de política e as atribuições relacionadas em uma base agendada para validar que a definição de política está atendendo às necessidades de conformidade e política de negócios. As políticas devem ser removidas se não forem mais necessárias. As políticas também precisam ser atualizadas de tempos em tempos à medida que os recursos subjacentes do Azure evoluem e adicionam novas propriedades e recursos.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [estrutura de definição de política](./definition-structure.md).
- Saiba mais sobre a [estrutura de atribuição de política](./assignment-structure.md).
- Entenda como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).