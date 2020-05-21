---
title: 'Padrão: Utilização de tags numa definição de política'
description: Este padrão de Política Azure fornece exemplos de como adicionar tags parametrizadas ou etiquetas herdatadas de um grupo de recursos numa definição de política.
ms.date: 05/20/2020
ms.topic: sample
ms.openlocfilehash: b71a21fadfc8dec3da2feabbce127303174a95d3
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704586"
---
# <a name="azure-policy-pattern-tags"></a>Padrão de política azure: tags

[As etiquetas](../../..//azure-resource-manager/management/tag-resources.md) são uma parte importante da gestão, organização e gestão dos seus recursos Azure. A Política Azure permite configurar etiquetas nos seus novos recursos existentes em escala com as tarefas [de modificação](../concepts/effects.md#modify) de efeito e [reparação.](../how-to/remediate-resources.md)

## <a name="sample-1-parameterize-tags"></a>Amostra 1: Parametrize as etiquetas

Esta definição de política utiliza dois parâmetros, **tagName** e **tagValue** para definir o que a atribuição de políticas procura em grupos de recursos. Este formato permite que a definição de política seja utilizada para qualquer número de combinações de nome de etiqueta e etiqueta, mas apenas manter uma definição de política única.

> [!NOTE]
> Embora este padrão de definição de política seja semelhante ao do [Padrão: Parâmetros - Amostra #1,](./pattern-parameters.md#sample-1-string-parameters)esta amostra utiliza o **modo** _All_ e visa grupos de recursos.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Amostra 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

Nesta amostra, o **modo** é definido para _Todos,_ uma vez que visa um grupo de recursos. Na maioria dos casos, o **modo** deve ser definido para _Indexado_ quando se trabalha com etiquetas. Para mais informações, consulte [os modos](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

Nesta parte da definição de política, `concat` combina o parâmetro de **tagName** parametrizado e o `tags['name']` formato para dizer campo para avaliar essa etiqueta para o parâmetro **tagValue**. **field**
Como **não** é utilizado, se **as tags \[ tags tags TagName \] ** não forem iguais **tagValue**, o efeito **modificador** é desencadeado.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

Aqui, o mesmo formato para utilizar os valores de etiqueta parametrizados é utilizado pela operação **addOrReplace** para criar ou atualizar a etiqueta para o valor desejado no grupo de recursos avaliados.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Amostra 2: Valor herdado do grupo de recursos

Esta definição de política utiliza o **nome** de parâmetro para determinar qual o valor da etiqueta para herdar do grupo de recursos-mãe.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Amostra 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

Nesta amostra, o **modo** é definido para _Indexado_ uma vez que não visa um grupo de recursos ou subscrição, mesmo que obtenha o valor de um grupo de recursos. Para mais informações, consulte [os modos](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

A **políticaRule.se** usa como Sample #1 para avaliar o valor do nome de `concat` **etiqueta,** mas usa a [Sample #1](#sample-1-parameterize-tags) `resourceGroup()` função para compará-lo com o valor da mesma etiqueta no grupo de recursos-mãe. A segunda cláusula aqui verifica que a etiqueta no grupo de recursos tem um valor e não é nula.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

Aqui, o valor atribuído à tag **Name** no recurso também utiliza a função para obter o valor do grupo de `resourceGroup()` recursos-mãe. Desta forma, pode herdar etiquetas de grupos de recursos parentais. Se já criou o recurso mas não adicionou a etiqueta, esta mesma definição de política e uma tarefa de [reparação](../how-to/remediate-resources.md) podem atualizar os recursos existentes.

## <a name="next-steps"></a>Próximos passos

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).