---
title: Avaliar o impacto de uma nova definição de Política Azure
description: Compreenda o processo a seguir ao introduzir uma nova definição de política no seu ambiente Azure.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 187a64ce3581c4aaa893e2a805ce787a0bfd6c79
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091723"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>Avaliar o impacto de uma nova definição de Política Azure

A Azure Policy é uma ferramenta poderosa para gerir os seus recursos Azure aos padrões de negócio e para atender às necessidades de conformidade. Quando pessoas, processos ou oleodutos criam ou atualizam recursos, a Azure Policy revê o pedido. Quando o efeito de definição de política é [Modificar,](./effects.md#modify) [Apêndice](./effects.md#deny)ou [ImplementarIfNotExists,](./effects.md#deployifnotexists)a política altera o pedido ou adiciona-lhe. Quando o efeito de definição de política é [Auditoria](./effects.md#audit) ou [AuditIfNotExists,](./effects.md#auditifnotexists)a política faz com que seja criada uma entrada de registo de atividade para novos recursos atualizados. E quando o efeito de definição de política é [Deny,](./effects.md#deny)a política para a criação ou alteração do pedido.

Estes resultados são exatamente como desejado quando sabemos que a política está corretamente definida. No entanto, é importante validar um novo trabalho político como pretendido antes de permitir que mude ou bloqueie o trabalho. A validação deve assegurar que apenas os recursos pretendidos sejam determinados como incompatíveis e que não sejam incluídos recursos conformes incorretamente (conhecidos como _falso positivos)_ nos resultados.

A abordagem recomendada para validar uma nova definição de política é seguindo estes passos:

- Defina bem a sua política
- Audite os seus recursos existentes
- Auditoria a pedidos de recursos novos ou atualizados
- Implemente a sua política em recursos
- Monitorização contínua

## <a name="tightly-define-your-policy"></a>Defina bem a sua política

É importante entender como a política de negócios é implementada como uma definição de política e a relação dos recursos da Azure com outros serviços Azure. Este passo é realizado [identificando os requisitos](../tutorials/create-custom-policy-definition.md#identify-requirements) e [determinando as propriedades dos recursos.](../tutorials/create-custom-policy-definition.md#determine-resource-properties)
Mas também é importante ver além da definição estreita da sua política de negócios. A sua política indica, por exemplo, "Todas as Máquinas Virtuais devem..."? E outros serviços Azure que utilizam VMs, como HDInsight ou AKS? Ao definir uma política, temos de refletir sobre o impacto desta política nos recursos utilizados por outros serviços.

Por esta razão, as suas definições políticas devem ser tão bem definidas e focadas nos recursos e propriedades que precisa para avaliar o mais possível para o cumprimento possível.

## <a name="audit-existing-resources"></a>Auditoria aos recursos existentes

Antes de procurar gerir recursos novos ou atualizados com a sua nova definição de política, o melhor é ver como avalia um subconjunto limitado de recursos existentes, como um grupo de recursos de teste. Utilize o [modo de execução](./assignment-structure.md#enforcement-mode) _Desativado_ (DoNotEnforce) na sua atribuição de política para evitar que o [efeito](./effects.md) desencadeie ou as entradas de registo de atividade sejam criadas.

Este passo dá-lhe a oportunidade de avaliar os resultados da conformidade da nova política sobre os recursos existentes sem afetar o fluxo de trabalho. Verifique se nenhum recursos em conformidade são marcados como incompatíveis _(falso positivo)_ e que todos os recursos que espera não estar em conformidade são marcados corretamente.
Após o subconjunto inicial de recursos validar como esperado, expandir lentamente a avaliação a todos os recursos existentes.

A avaliação dos recursos existentes deste modo constitui também uma oportunidade para remediar recursos não conformes antes da plena implementação da nova política. Esta limpeza pode ser feita manualmente ou através de uma [tarefa de remediação](../how-to/remediate-resources.md) se o efeito de definição de política for _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Auditoria de recursos novos ou atualizados

Uma vez validado a sua nova definição de política está a reportar corretamente sobre os recursos existentes, é hora de olhar para o impacto da política quando os recursos são criados ou atualizados. Se a definição de política apoiar a parametrização do efeito, utilize [a Auditoria](./effects.md#audit). Esta configuração permite-lhe monitorizar a criação e atualização de recursos para ver se a nova definição de política desencadeia uma entrada no registo de AtividadeS Azure para um recurso que não está em conformidade sem afetar o trabalho ou pedidos existentes.

É recomendado tanto atualizar como criar novos recursos que correspondam à sua definição de política para ver se o efeito _De auditoria_ está a ser corretamente acionado quando esperado. Esteja atento a pedidos de recursos que não devem ser impactados pela nova definição de política que desencadeie o efeito _Auditoria._
Estes recursos impactados são mais um exemplo de _falsos positivos_ e devem ser fixados na definição de política antes da plena implementação.

No caso de a definição de política ser alterada nesta fase de testes, recomenda-se iniciar o processo de validação com a auditoria dos recursos existentes. Uma alteração da definição de política para um _falso positivo_ sobre recursos novos ou atualizados é suscetível de ter também um impacto nos recursos existentes.

## <a name="deploy-your-policy-to-resources"></a>Implemente a sua política em recursos

Após completar a validação da sua nova definição de política com recursos existentes e pedidos de recursos novos ou atualizados, inicia o processo de implementação da política. É recomendado criar a atribuição de políticas para a nova definição de política a um subconjunto de todos os recursos em primeiro lugar, como um grupo de recursos. Após validação da implementação inicial, alargar o âmbito da política a níveis mais amplos e mais amplos, tais como subscrições e grupos de gestão. Esta expansão é conseguida removendo a atribuição e criando uma nova nos âmbitos-alvo até que seja atribuída a todo o âmbito de recursos destinados a ser cobertos pela sua nova definição de política.

Durante o lançamento, se estiverem localizados recursos que devam ser isentos da sua nova definição de política, dirija-se a eles de uma das seguintes formas:

- Atualizar a definição de política para ser mais explícito para reduzir o impacto não intencional
- Alterar o âmbito da atribuição de políticas (removendo e criando uma nova atribuição)
- Adicione o grupo de recursos à lista de exclusão para a atribuição de políticas

Quaisquer alterações ao âmbito (nível ou exclusões) devem ser totalmente validadas e comunicadas com as suas organizações de segurança e conformidade para garantir que não existem lacunas na cobertura.

## <a name="monitor-your-policy-and-compliance"></a>Monitorize a sua política e conformidade

Implementar e atribuir a sua definição de política não é o passo final. Monitorize continuamente o nível de [conformidade](../how-to/get-compliance-data.md) dos recursos à sua nova definição de política e configurar [os alertas e notificações adequados do Azure Monitor](../../../azure-monitor/alerts/alerts-overview.md) para quando forem identificados dispositivos não conformes. Também é recomendado avaliar a definição de política e atribuições relacionadas numa base programada para validar a definição de política está a satisfazer as necessidades de negócio e conformidade. As políticas devem ser eliminadas, se já não for necessário. As políticas também precisam de ser atualizadas de tempos a tempos à medida que os recursos Azure subjacentes evoluem e adicionam novas propriedades e capacidades.

## <a name="next-steps"></a>Passos seguintes

- Conheça a estrutura de [definição de políticas.](./definition-structure.md)
- Conheça a [estrutura de atribuição de políticas.](./assignment-structure.md)
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)