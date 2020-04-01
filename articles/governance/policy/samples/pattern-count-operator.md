---
title: 'Padrão: O operador de contagem numa definição de política'
description: Este padrão de política azure fornece um exemplo de como utilizar o operador de contagem numa definição de política.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172948"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Padrão de política azure: o operador de contagem

O operador [da contagem](../concepts/definition-structure.md#count) \[ \* \] avalia membros de um pseudónimo.

## <a name="sample-policy-definition"></a>Definição de política de amostras

Esta definição de política [audita grupos](../concepts/effects.md#audit) de segurança da rede configurados para permitir o tráfego de protocolo de ambiente de trabalho remoto (RDP) de entrada.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Explicação

Os componentes do núcleo do operador de **contagem** são _de campo,_ _onde_e a condição. Cada um é realçado no corte abaixo.

- _campo_ conta contagem que [pseudónimo](../concepts/definition-structure.md#aliases) para avaliar membros de. Aqui, estamos a ver o _conjunto_ de regras de **segurança\[ \* ** do grupo de segurança da rede.
- _onde_ utiliza a linguagem política para definir quais os membros da _matriz_ que satisfazem os critérios. Neste exemplo, um operador todo **lógico** agrupa três diferentes avaliações de condições de propriedades de _matriz_ de pseudónimos: _direção,_ _acesso,_ e _destinoPortRange_.
- A condição de contagem neste exemplo é **maior.** A contagem avalia como verdadeira quando um ou mais membros da _matriz_ de pseudónimos correspondem à cláusula _de sempre._

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Passos seguintes

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).