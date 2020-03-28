---
title: 'Padrão: Operadores lógicos numa definição de política'
description: Este padrão de política azure fornece exemplos de como utilizar os operadores lógicos numa definição de política.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 8e57efaea81848c6b2d0188dbf3f91e06ed74c67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172850"
---
# <a name="azure-policy-pattern-logical-operators"></a>Padrão de política azure: operadores lógicos

Uma definição de política pode conter várias declarações condicionais. Pode precisar que cada declaração seja verdadeira ou só precisa de algumas para ser verdade. Para suportar estas necessidades, a linguagem tem [operadores lógicos](../concepts/definition-structure.md#logical-operators) para **não**, **todos os todo**, e qualquer um **.** São opcionais e podem ser aninhados para criar cenários complexos.

## <a name="sample-1-one-logical-operator"></a>Amostra 1: Um operador lógico

Esta definição de política avalia as contas da CosmosDB para ver se as falhas automáticas e os vários locais de escrita estão configurados. Quando não estão, a [auditoria](../concepts/effects.md#audit) dispara e cria uma entrada de registo quando o recurso não conforme é criado ou atualizado.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Amostra 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

A **políticaRule.se** o bloco usar um único **allOf** para garantir que as três condições são verdadeiras.
Só quando todas estas condições avaliam verdadeiramente é que o efeito **de auditoria** dispara.

## <a name="sample-2-multiple-logical-operators"></a>Amostra 2: Múltiplos operadores lógicos

Esta definição de política avalia os recursos para um padrão de nomeação. Se um recurso não corresponder, é [negado.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Amostra 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Esta **políticaRule.se** o bloco também inclui um único **allOf**, mas cada condição é embrulhada com o operador **não** lógico. O condicional dentro do operador **não** lógico avalia primeiro e, em seguida, avalia o **não** determinar se a cláusula inteira é verdadeira ou falsa. Se ambos **os** operadores não lógicos avaliarem verdadeiramente, o efeito político desencadeia.

## <a name="next-steps"></a>Passos seguintes

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).