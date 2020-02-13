---
title: 'Padrão: O operador de valor numa definição de política'
description: Este padrão de Política Azure fornece um exemplo de como utilizar o operador de valor numa definição de política.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: ace7b7cd4a765cdb8c7aa764b52b180c60508ab2
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172787"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Padrão de política azure: o operador de valor

O operador de [valor](../concepts/definition-structure.md#value) avalia [parâmetros,](../concepts/definition-structure.md#parameters) [funções de modelo suportado,](../concepts/definition-structure.md#policy-functions)ou literais a um valor fornecido para uma determinada [condição.](../concepts/definition-structure.md#conditions)

> [!WARNING]
> Se o resultado de uma _função_ de modelo for um erro, a avaliação da política falha. Uma avaliação falhada é um **negação**implícito. Para mais informações, consulte [evitar falhas](../concepts/definition-structure.md#avoiding-template-failures)no modelo .

## <a name="sample-policy-definition"></a>Definição de política de amostras

Esta definição de política adiciona ou substitui a etiqueta especificada no **nome** do parâmetro _(string_) nos recursos e herda o valor para **tagName** do grupo de recursos em que o recurso se encontra. Esta avaliação acontece quando o recurso é criado ou atualizado. Como efeito [modificado,](../concepts/effects.md#modify) a reparação pode ser efetuada sobre os recursos existentes através de uma tarefa de [reparação](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

O operador de **valor** é utilizado dentro da **políticaRule.if** block dentro **de propriedades**. Neste exemplo, o [operador lógico](../concepts/definition-structure.md#logical-operators) **é** utilizado para afirmar que ambas as declarações condicionais devem ser verdadeiras para o efeito, **modificar,** ter lugar.

**valor** avalia o resultado do recurso de função do [modeloGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) à condição **não igual a** um valor em branco. Se o nome da etiqueta fornecido no **tagName** no grupo de recursos-mãe existir, o condicional avalia-o de verdade.

## <a name="next-steps"></a>Passos seguintes

- Reveja outros [padrões e definições incorporadas.](./index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).