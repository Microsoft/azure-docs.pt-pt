---
title: 'Padrão: Operadores lógicos numa definição de política'
description: Este padrão de política azure fornece exemplos de como utilizar os operadores lógicos numa definição de política.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 691383b1f8ae34bbd51ce7f4f9310980e3c66537
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272513"
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

## <a name="sample-3-combining-logical-operators"></a>Amostra 3: Combinação de operadores lógicos

Esta definição de política avalia as contas de Java Spring para ver se algum dos vestígios não está ativado ou se o vestígio não está num estado de sucesso.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Amostra 3: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Esta **políticaRule.se** o bloco inclui tanto o **allOf** como **qualquer dos** operadores lógicos. O operador lógico **avalia** a verdade enquanto uma condição incluída for verdadeira. Como o _tipo_ está no centro do **allOf,** deve sempre avaliar o verdadeiro. Se o _tipo_ e uma das condições em **qualquer Of** forem verdadeiros, o efeito político dispara.

## <a name="next-steps"></a>Passos seguintes

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).