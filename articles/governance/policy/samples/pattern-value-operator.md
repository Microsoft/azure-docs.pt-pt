---
title: 'Padrão: O operador de valor numa definição de política'
description: Este padrão de Política Azure fornece um exemplo de como usar o operador de valor numa definição de política.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: 560f128dc5f78ca2335f2712e7fd81bd94eda761
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092803"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Padrão de política Azure: o operador de valor

O operador de [valor](../concepts/definition-structure.md#value) avalia [parâmetros,](../concepts/definition-structure.md#parameters) [funções de modelo suportadas,](../concepts/definition-structure.md#policy-functions)ou literais a um valor fornecido para uma determinada [condição](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Se o resultado de uma _função de modelo_ for um erro, a avaliação da política falha. Uma avaliação falhada é uma **negação** implícita. Para obter mais informações, consulte [evitar falhas no modelo](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Definição de política de amostra

Esta definição de política adiciona ou substitui a etiqueta especificada no conjunto de **parâmetrosName** _(string_) sobre os recursos e herda o valor para o nome de **tagName** do grupo de recursos em que o recurso se encontra. Esta avaliação acontece quando o recurso é criado ou atualizado. Como efeito [de modificação,](../concepts/effects.md#modify) a reparação pode ser executada sobre os recursos existentes através de uma [tarefa de reparação](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

O operador de **valor** é utilizado dentro da **apóliceRule.se** bloquear dentro de **propriedades**. Neste exemplo, o [operador lógico](../concepts/definition-structure.md#logical-operators) **allOf** é utilizado para afirmar que ambas as declarações condicionais devem ser verdadeiras para o efeito, **modificar,** a ocorrer.

**valor** avalia o resultado do grupo de recursos de função do modelo [à](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) condição **não Equals** de um valor em branco. Se o nome da etiqueta fornecido no **nome de tagname** do grupo de recursos-mãe existir, o condicional avalia a verdade.

## <a name="next-steps"></a>Passos seguintes

- Reveja [outros padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).