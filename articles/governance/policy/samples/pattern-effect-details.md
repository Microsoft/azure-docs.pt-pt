---
title: 'Padrão: Efeitos de uma definição de política'
description: Este padrão de política azure fornece um exemplo de como usar os diferentes efeitos de uma definição de política.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 1a9aec50bd328b76271d54f7830c75e0848d3cde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80372642"
---
# <a name="azure-policy-pattern-effects"></a>Padrão de política azure: efeitos

A Política Azure tem uma série de [efeitos](../concepts/effects.md) que determinam como o serviço reage a recursos não conformes. Alguns efeitos são simples e não requerem propriedades adicionais na definição de política, enquanto outros requerem várias propriedades.

## <a name="sample-1-simple-effect"></a>Amostra 1: Efeito simples

Esta definição de política verifica se a etiqueta definida no parâmetro **tagName** existe no recurso avaliado. Se a etiqueta ainda não existir, o efeito [modificador](../concepts/effects.md#modify) é acionado para adicionar a etiqueta com o valor na etiqueta do **parâmetroValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Amostra 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Um efeito **modificador** requer a **políticaRule.then.details** block que define **roleDefinitionIds** e **operações**. Estes parâmetros informam a Política Azure sobre as funções necessárias para adicionar a etiqueta e remediar o recurso e que **modificam** a operação para executar. Neste exemplo, a **operação** é _acrescentada_ e os parâmetros são utilizados para definir a etiqueta e o seu valor.

## <a name="sample-2-complex-effect"></a>Amostra 2: Efeito complexo

Esta definição de política audita cada máquina virtual para quando uma extensão, definida em parâmetros **editor** e **tipo,** não existe. Utiliza [auditoriaIfNotExists](../concepts/effects.md#auditifnotexists) para verificar um recurso relacionado com a máquina virtual para ver se existe uma instância que corresponda aos parâmetros definidos. Este exemplo verifica o tipo de **extensões.**

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Amostra 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Um efeito **auditIfNotExists** requer a **políticaRule.then.details** block para definir tanto um **tipo** como a **existênciaCondição** para procurar. A **existênciaCondition** utiliza elementos de linguagem política, tais como [operadores lógicos,](../concepts/definition-structure.md#logical-operators)para determinar se existe um recurso relacionado com correspondência. Neste exemplo, os valores verificados em relação a cada [pseudónimo](../concepts/definition-structure.md#aliases) são definidos em parâmetros.

## <a name="next-steps"></a>Passos seguintes

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).