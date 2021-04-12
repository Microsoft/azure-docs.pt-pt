---
title: 'Padrão: Usar etiquetas numa definição de política'
description: Este padrão de Política Azure fornece exemplos de como adicionar tags parametrizadas ou herdar tags de um grupo de recursos numa definição de política.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: c748eb9b8ea795f9725082ec0aa0b8065ada8c65
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093389"
---
# <a name="azure-policy-pattern-tags"></a>Padrão de política azul: tags

[As etiquetas](../../..//azure-resource-manager/management/tag-resources.md) são uma parte importante da gestão, organização e governação dos seus recursos Azure. A Política Azure permite configurar etiquetas nos seus novos recursos existentes em escala com o efeito [de modificação](../concepts/effects.md#modify) e [as tarefas de remediação](../how-to/remediate-resources.md).

## <a name="sample-1-parameterize-tags"></a>Amostra 1: Parametrizar tags

Esta definição de política utiliza dois parâmetros, **tagName** e **tagValue** para definir o que a atribuição de política procura em grupos de recursos. Este formato permite que a definição de política seja usada para qualquer número de combinações de etiquetas e valor de etiqueta, mas apenas manter uma definição de política única.

> [!NOTE]
> Embora este padrão de definição de política seja semelhante ao de [Pattern: Parâmetros - Sample #1](./pattern-parameters.md#sample-1-string-parameters), esta amostra utiliza **o modo** _All_ e visa grupos de recursos.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Amostra 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

Nesta amostra, **o modo** é definido para _All_ uma vez que visa um grupo de recursos. Na maioria dos casos, **o modo** deve ser definido para _Indexado_ ao trabalhar com etiquetas. Para obter mais informações, consulte [os modos](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

Nesta parte da definição de política, `concat` combina o parâmetro de **tagName** parametrizado e o `tags['name']` formato para dizer ao **campo** para avaliar essa etiqueta para o **parâmetro tagValue**.
Como **não** é utilizado, se **as tags \[ tagName \]** não forem iguais ao **tagValue,** o efeito **de modificação** é desencadeado.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

Aqui, o mesmo formato para a utilização dos valores de etiqueta parametrizada é utilizado pela operação **addOrReplace** para criar ou atualizar a etiqueta para o valor pretendido no grupo de recursos avaliado.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Amostra 2: Herdar o valor da etiqueta do grupo de recursos

Esta definição de política utiliza o **nome de parâmetroname** para determinar qual o valor da etiqueta a herdar do grupo de recursos-mãe.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Amostra 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

Nesta amostra, o **modo** é definido para _Indexado_ uma vez que não visa um grupo de recursos ou subscrição, mesmo que obtenha o valor de um grupo de recursos. Para obter mais informações, consulte [os modos](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

A **regra de política.se** utiliza `concat` como sample [#1](#sample-1-parameterize-tags) para avaliar o valor do nome **de marca,** mas utiliza a `resourceGroup()` função para compará-la com o valor da mesma etiqueta no grupo de recursos dos pais. A segunda cláusula aqui verifica que a etiqueta no grupo de recursos tem um valor e não é nula.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

Aqui, o valor atribuído à etiqueta **tagName** no recurso também utiliza a `resourceGroup()` função para obter o valor do grupo de recursos dos pais. Desta forma, pode herdar etiquetas de grupos de recursos dos pais. Se já criou o recurso mas não adicionou a etiqueta, esta mesma definição de política e uma [tarefa de reparação](../how-to/remediate-resources.md) podem atualizar os recursos existentes.

## <a name="next-steps"></a>Passos seguintes

- Reveja [outros padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).