---
title: 'Padrão: Efeitos de uma definição de política'
description: Este padrão de Política Azure fornece um exemplo de como utilizar os diferentes efeitos de uma definição de política.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: f1da9bd153707db35c07ed3c176542797a694d7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92073041"
---
# <a name="azure-policy-pattern-effects"></a>Padrão de política azul: efeitos

A Azure Policy tem uma série de efeitos que [determinam](../concepts/effects.md) como o serviço reage a recursos não conformes. Alguns efeitos são simples e não requerem propriedades adicionais na definição de política, enquanto outros requerem várias propriedades.

## <a name="sample-1-simple-effect"></a>Amostra 1: Efeito simples

Esta definição de política verifica se a etiqueta definida no **nome de parâmetrosName** existe no recurso avaliado. Se a etiqueta ainda não existir, o efeito [de modificação](../concepts/effects.md#modify) é acionado para adicionar a etiqueta com o valor no **parâmetro tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Amostra 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Um efeito **de modificação** requer o **bloco policyRule.then.details** que define **funDefinitionIds** e **operações**. Estes parâmetros informam a Azure Policy quais as funções necessárias para adicionar a etiqueta e remediar o recurso e que **modificam** a operação a utilizar. Neste exemplo, a **operação** _de adição_ e os parâmetros são utilizados para definir a etiqueta e o seu valor.

## <a name="sample-2-complex-effect"></a>Amostra 2: Efeito complexo

Esta definição de política audita cada máquina virtual para quando uma extensão, definida em **parâmetros editor** e **tipo,** não existe. Utiliza [auditoriaIfNotExists](../concepts/effects.md#auditifnotexists) para verificar um recurso relacionado com a máquina virtual para ver se existe uma instância que corresponda aos parâmetros definidos. Este exemplo verifica o tipo **de extensões.**

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Amostra 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Um efeito **auditIfNotExists** requer o bloco **policyRule.then.details** para definir tanto um **tipo** como a **existênciaCondição** a procurar. A **existência A condição** utiliza elementos linguísticos políticos, como [operadores lógicos,](../concepts/definition-structure.md#logical-operators)para determinar se existe um recurso relacionado com a correspondência. Neste exemplo, os valores verificados contra cada pseudónimo são [definidos](../concepts/definition-structure.md#aliases) em parâmetros.

## <a name="next-steps"></a>Passos seguintes

- Reveja [outros padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).