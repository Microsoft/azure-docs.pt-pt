---
title: 'Padrão: Operadores lógicos numa definição de política'
description: Este padrão de Política Azure fornece exemplos de como utilizar os operadores lógicos numa definição de política.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 3f644cdbfc45b06d1ad5db8e7727c0fa69742f00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545595"
---
# <a name="azure-policy-pattern-logical-operators"></a>Padrão de política Azure: operadores lógicos

Uma definição de política pode conter várias declarações condicionais. Pode precisar que cada declaração seja verdadeira ou só precise que algumas sejam verdadeiras. Para suportar estas necessidades, a língua tem [operadores lógicos](../concepts/definition-structure.md#logical-operators) para **não**, **allOf**, e **qualquer**. São opcionais e podem ser aninhados para criar cenários complexos.

## <a name="sample-1-one-logical-operator"></a>Amostra 1: Um operador lógico

Esta definição de política avalia as contas do CosmosDB para ver se as falhas automáticas e vários locais de escrita estão configurados. Quando não estão, a [auditoria](../concepts/effects.md#audit) dispara e cria uma entrada de registo quando o recurso não conforme é criado ou atualizado.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Amostra 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

A **regra políticaRule.se** o bloco usar um único **allOf** para garantir que as três condições são verdadeiras.
Só quando todas estas condições avaliam verdadeiramente o efeito **de auditoria** é que desencadeia.

## <a name="sample-2-multiple-logical-operators"></a>Amostra 2: Múltiplos operadores lógicos

Esta definição de política avalia os recursos para um padrão de nomeação. Se um recurso não corresponder, é [negado.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Amostra 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Esta **apóliceRule.se** o bloco também inclui um único **allOf**, mas cada condição é embrulhada com o operador **não** lógico. O condicionamento dentro do operador **não** lógico avalia primeiro e, em seguida, avalia o **não** determinar se toda a cláusula é verdadeira ou falsa. Se ambos os operadores **não** lógicos avaliarem a verdade, o efeito político dispara.

## <a name="sample-3-combining-logical-operators"></a>Amostra 3: Combinar operadores lógicos

Esta definição de política avalia as contas de Java Spring para ver se algum dos vestígios não está habilitado ou se o rastreio não está em um estado de sucesso.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Amostra 3: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Esta **apóliceRule.se** o bloco inclui tanto o **allOf como** **qualquer** operador lógico. O **operador** lógico avalia a verdade desde que uma condição incluída seja verdadeira. Como o _tipo_ está no centro do **allOf,** deve sempre avaliar a verdade. Se o _tipo_ e uma das condições no **reino** são verdadeiros, o efeito político dispara.

## <a name="next-steps"></a>Passos seguintes

- Reveja [outros padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).