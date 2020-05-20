---
title: Avaliar o impacto de uma nova definição de Política Azure
description: Compreenda o processo a seguir ao introduzir uma nova definição política no seu ambiente Azure.
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: d9844e8435452b388c934c5969898fe01d23fb47
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684295"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>Avaliar o impacto de uma nova definição de Política Azure

A Azure Policy é uma ferramenta poderosa para gerir os seus recursos Azure para padrões de negócio e para satisfazer as necessidades de conformidade. Quando pessoas, processos ou oleodutos criam ou atualizam recursos, a Política Azure revê o pedido. Quando o efeito de definição de política é [apêndice](./effects.md#deny) ou [implementação,](./effects.md#deployifnotexists)a política altera o pedido ou adiciona-lhe. Quando o efeito de definição de política é [Auditoria](./effects.md#audit) ou [AuditoriaIfNotExists,](./effects.md#auditifnotexists)a política faz com que seja criada uma entrada de registo de atividade. E quando o efeito de definição de política é [Deny,](./effects.md#deny)a política para a criação ou alteração do pedido.

Estes resultados são exatamente os desejados quando se sabe que a política é definida corretamente. No entanto, é importante validar uma nova política como pretendido antes de permitir que mude ou bloqueie o trabalho. A validação deve assegurar que apenas os recursos pretendidos sejam determinados como incompatíveis e que não sejam incluídos incorretamente recursos conformes (conhecidos como _falsos positivos)_ nos resultados.

A abordagem recomendada para validar uma nova definição de política é seguindo estes passos:

- Defina firmemente a sua política
- Audite os seus recursos existentes
- Auditar novos ou atualizados pedidos de recursos
- Implemente a sua política em recursos
- Monitorização contínua

## <a name="tightly-define-your-policy"></a>Defina firmemente a sua política

É importante entender como a política de negócios é implementada como uma definição de política e a relação dos recursos azure com outros serviços Azure. Este passo é realizado [identificando os requisitos](../tutorials/create-custom-policy-definition.md#identify-requirements) e [determinando as propriedades dos recursos.](../tutorials/create-custom-policy-definition.md#determine-resource-properties)
Mas também é importante ver para além da definição estreita da sua política comercial. O seu estado político, por exemplo, "Todas as máquinas virtuais devem..."? E outros serviços Azure que utilizam VMs, como hDInsight ou AKS? Ao definirmos uma política, temos de considerar o impacto desta política nos recursos utilizados por outros serviços.

Por esta razão, as definições políticas devem ser tão bem definidas e focadas nos recursos e propriedades que necessita para avaliar o cumprimento possível.

## <a name="audit-existing-resources"></a>Auditoria dos recursos existentes

Antes de procurar gerir recursos novos ou atualizados com a sua nova definição de política, o melhor é ver como avalia um subconjunto limitado de recursos existentes, como um grupo de recursos de teste. Utilize o modo de [execução](./assignment-structure.md#enforcement-mode) _Desativado_ (DoNotEnforce) na sua atribuição de política para evitar que o [efeito](./effects.md) desencadeie ou que sejam criadas entradas de registo de atividade.

Este passo dá-lhe a oportunidade de avaliar os resultados de conformidade da nova política sobre os recursos existentes sem afetar o fluxo de trabalho. Verifique se nenhum recursos conformes são marcados como não conformes _(falsopositivos)_ e que todos os recursos que espera não cumprir são marcados corretamente.
Após o subconjunto inicial de recursos validar como esperado, expanda lentamente a avaliação a todos os recursos existentes.

A avaliação dos recursos existentes desta forma constitui também uma oportunidade para remediar os recursos não conformes antes da plena implementação da nova política. Esta limpeza pode ser feita manualmente ou através de uma tarefa de [reparação](../how-to/remediate-resources.md) se o efeito de definição de política for _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Auditar recursos novos ou atualizados

Uma vez validada a sua nova definição de política está a reportar corretamente sobre os recursos existentes, é hora de olhar para o impacto da política quando os recursos são criados ou atualizados. Se a definição de política apoiar a parametrização do efeito, utilize [a Auditoria](./effects.md#audit). Esta configuração permite-lhe monitorizar a criação e atualização de recursos para ver se a nova definição de política desencadeia uma entrada no registo da Atividade Azure para um recurso que não está em conformidade sem afetar o trabalho ou os pedidos existentes.

Recomenda-se tanto atualizar e criar novos recursos que correspondam à sua definição política para ver se o efeito _Auditoria_ está a ser corretamente desencadeado quando esperado. Esteja atento a pedidos de recursos que não devem ser impactados pela nova definição de política que desencadeia o efeito _Auditoria._
Estes recursos impactados são mais um exemplo de _falsos positivos_ e devem ser fixados na definição de política antes da plena implementação.

No caso de a definição de política ser alterada nesta fase de testes, é aconselhável iniciar o processo de validação com a auditoria dos recursos existentes. É provável que uma alteração da definição de política para um _falso positivo_ sobre recursos novos ou atualizados também tenha um impacto nos recursos existentes.

## <a name="deploy-your-policy-to-resources"></a>Implemente a sua política em recursos

Depois de concluir a validação da sua nova definição de política com recursos existentes e pedidos de recursos novos ou atualizados, inicia o processo de implementação da política. É recomendado criar a atribuição de políticas para a nova definição de política para um subconjunto de todos os recursos em primeiro lugar, como um grupo de recursos. Após a validação da implantação inicial, alargar o âmbito da política a níveis mais amplos e mais amplos, tais como assinaturas e grupos de gestão. Esta expansão é conseguida removendo a atribuição e criando uma nova nos âmbitos-alvo até que seja atribuída ao âmbito completo dos recursos destinados a ser cobertos pela sua nova definição de política.

Durante o lançamento, se estiverem localizados recursos que devam estar isentos da sua nova definição de política, dirija-os de uma das seguintes formas:

- Atualizar a definição de política para ser mais explícito para reduzir o impacto não intencional
- Alterar o âmbito da atribuição de políticas (removendo e criando uma nova atribuição)
- Adicione o grupo de recursos à lista de exclusão para a atribuição de políticas

Quaisquer alterações ao âmbito (nível ou exclusões) devem ser totalmente validadas e comunicadas com as suas organizações de segurança e conformidade para garantir que não existem lacunas na cobertura.

## <a name="monitor-your-policy-and-compliance"></a>Monitorize a sua política e conformidade

Implementar e atribuir a sua definição de política não é o passo final. Monitorize continuamente o nível de [conformidade](../how-to/get-compliance-data.md) dos recursos à sua nova definição de política e configurar [alertas e notificações](../../../azure-monitor/platform/alerts-overview.md) adequadas do Monitor Do Azure para quando os dispositivos não conformes forem identificados. Também é recomendado avaliar a definição de política e as atribuições relacionadas numa base programada para validar a definição de política é atender às necessidades de política de negócios e conformidade. As políticas devem ser suprimidas se já não for necessário. As políticas também precisam de ser atualizadas de tempos a tempos à medida que os recursos azure subjacentes evoluem e acrescentam novas propriedades e capacidades.

## <a name="next-steps"></a>Passos seguintes

- Conheça a estrutura da definição de [políticas.](./definition-structure.md)
- Conheça a estrutura de atribuição de [políticas.](./assignment-structure.md)
- Compreender como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como obter dados de [conformidade.](../how-to/get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](../how-to/remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)