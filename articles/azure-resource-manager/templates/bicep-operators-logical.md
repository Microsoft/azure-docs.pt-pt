---
title: Operadores lógicos bicep
description: Descreve operadores lógicos bicep que avaliam as condições.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211337"
---
# <a name="bicep-logical-operators"></a>Operadores lógicos bicep

Os operadores lógicos avaliam valores boolean, devolvem valores não nulos ou avaliam uma expressão condicional. Para executar os exemplos, utilize o Azure CLI ou o Azure PowerShell para [implementar um ficheiro Bicep](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operador | Name |
| ---- | ---- |
| `&&` | [E](#and-) |
| `||` | [Ou](#or-) |
| `!` | [Não](#not-) |
| `??` | [Coalesce](#coalesce-) |
| `?` `:` | [Expressão condicional](#conditional-expression--) |

## <a name="and-"></a>E &&

`operand1 && operand2`

Determina se ambos os valores são verdadeiros.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | O primeiro valor para verificar se é verdade. |
| `operand2` | boolean | O segundo valor para verificar se é verdade. |
| Mais óperas | boolean | Mais óperas podem ser incluídas. |

### <a name="return-value"></a>Valor devolvido

`True` quando ambos os valores são verdadeiros, caso contrário `false` é devolvido.

### <a name="example"></a>Exemplo

Avalia um conjunto de valores de parâmetros e um conjunto de expressões.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>Ou ||

`operand1 || operand2`

Determina se um dos valores é verdadeiro.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | O primeiro valor para verificar se é verdade. |
| `operand2` | boolean | O segundo valor para verificar se é verdade. |
| Mais óperas | boolean | Mais óperas podem ser incluídas. |

### <a name="return-value"></a>Valor devolvido

`True` quando qualquer valor é verdadeiro, caso contrário `false` é devolvido.

### <a name="example"></a>Exemplo

Avalia um conjunto de valores de parâmetros e um conjunto de expressões.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Não, não, não!

`!boolValue`

Nega um valor booleano.

### <a name="operand"></a>Ópera

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `boolValue` | boolean | Valor booleano que é negado. |

### <a name="return-value"></a>Valor devolvido

Nega o valor inicial e devolve um booleano. Se o valor inicial `true` for, então `false` é devolvido.

### <a name="example"></a>Exemplo

O `not` operador nega um valor. Os valores podem ser embrulhados com parênteses.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `startedTrue` | boolean | false |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>Coalesce ??

`operand1 ?? operand2`

Devolve primeiro valor não nulo dos óperas.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | corda, inteiro, boolean, objeto, matriz | Valor a testar para `null` . |
| `operand2` | corda, inteiro, boolean, objeto, matriz | Valor a testar para `null` . |
| Mais óperas | corda, inteiro, boolean, objeto, matriz | Valor a testar para `null` . |

### <a name="return-value"></a>Valor devolvido

Devolve o primeiro valor não nulo. Cordas vazias, matrizes vazias e objetos vazios não `null` são e um valor é \<empty> devolvido.

### <a name="example"></a>Exemplo

As declarações de saída devolvem os valores não nulos. O tipo de saída deve corresponder ao tipo na comparação ou é gerado um erro.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `nonNullStr` | string | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | string | \<empty> |

## <a name="conditional-expression--"></a>Expressão condicional? :

`condition ? true-value : false-value`

Avalia uma condição e devolve um valor se a condição é verdadeira ou falsa.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `condition` | boolean | Condição para avaliar como verdadeiro ou falso. |
| `true-value` | corda, inteiro, boolean, objeto, matriz | Valor quando a condição é verdadeira. |
| `false-value` | corda, inteiro, boolean, objeto, matriz | Valor quando a condição é falsa. |

### <a name="example"></a>Exemplo

Este exemplo avalia a inicial de um parâmetro e devolve um valor se a condição é verdadeira ou falsa.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `outValue` | string | valor verdadeiro |

## <a name="next-steps"></a>Passos seguintes

- Para criar um ficheiro Bicep, consulte [Tutorial: Crie e implemente primeiro o ficheiro Bicep do Gestor de Recursos Azure](bicep-tutorial-create-first-bicep.md).
- Para obter informações sobre como resolver erros do tipo Bicep, consulte [Qualquer função para Bicep](template-functions-any.md).
- Para comparar sintaxe para Bicep e JSON, consulte [Comparar JSON e Bicep para modelos](compare-template-syntax.md).
- Para exemplos de funções do modelo Bicep e ARM, consulte [as funções do modelo ARM](template-functions.md).
