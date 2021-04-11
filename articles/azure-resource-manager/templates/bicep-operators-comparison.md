---
title: Operadores de comparação de bíceps
description: Descreve operadores de comparação Bicep que comparam valores.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c382ef355131d271d9f4fa4a978a807b9aac1e4f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211321"
---
# <a name="bicep-comparison-operators"></a>Operadores de comparação de bíceps

Os operadores de comparação comparam valores e `true` devolvem ou `false` . Para executar os exemplos, utilize o Azure CLI ou o Azure PowerShell para [implementar um ficheiro Bicep](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operador | Name |
| ---- | ---- |
| `>=` | [Maior ou igual a](#greater-than-or-equal-) |
| `>`  | [Maior que](#greater-than-) |
| `<=` | [Menor ou igual a](#less-than-or-equal-) |
| `<`  | [Menos de](#less-than-) |
| `==` | [Igual a](#equals-) |
| `!=` | [Diferente](#not-equal-) |
| `=~` | [Caso igual-insensível](#equal-case-insensitive-) |
| `!~` | [Não igual caso-insensível](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Maior ou igual >=

`operand1 >= operand2`

Avalia se o primeiro valor é maior ou igual ao segundo valor.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | inteiro, corda | Primeiro valor na comparação. |
| `operand2` | inteiro, corda | Segundo valor na comparação. |

### <a name="return-value"></a>Valor devolvido

Se o primeiro valor for superior ou igual ao segundo valor, `true` é devolvido. Caso contrário, `false` será devolvido.

### <a name="example"></a>Exemplo

Um par de inteiros e um par de cordas são comparados.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>Maior que >

`operand1 > operand2`

Avalia se o primeiro valor é maior do que o segundo valor.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | inteiro, corda | Primeiro valor na comparação. |
| `operand2` | inteiro, corda | Segundo valor na comparação. |

### <a name="return-value"></a>Valor devolvido

Se o primeiro valor for maior do que o segundo valor, `true` é devolvido. Caso contrário, `false` será devolvido.

### <a name="example"></a>Exemplo

Um par de inteiros e um par de cordas são comparados.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

Saída a partir do exemplo:

O **e** em **curva** torna a primeira corda maior.

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>Menos ou igual <=

`operand1 <= operand2`

Avalia se o primeiro valor é inferior ou igual ao segundo valor.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | inteiro, corda | Primeiro valor na comparação. |
| `operand2` | inteiro, corda | Segundo valor na comparação. |

### <a name="return-value"></a>Valor devolvido

Se o primeiro valor for inferior ou igual ao segundo valor, `true` é devolvido. Caso contrário, `false` será devolvido.

### <a name="example"></a>Exemplo

Um par de inteiros e um par de cordas são comparados.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>Menos de <

`operand1 < operand2`

Avalia se o primeiro valor é inferior ao segundo valor.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | inteiro, corda | Primeiro valor na comparação. |
| `operand2` | inteiro, corda | Segundo valor na comparação. |

### <a name="return-value"></a>Valor devolvido

Se o primeiro valor for inferior ao segundo valor, `true` é devolvido. Caso contrário, `false` será devolvido.

### <a name="example"></a>Exemplo

Um par de inteiros e um par de cordas são comparados.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

Saída a partir do exemplo:

A corda é `true` porque letras minúsculas são menos do que letras maiúsculas.

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>Igual ==

`operand1 == operand2`

Avalia se os valores são iguais.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | corda, inteiro, boolean, matriz, objeto | Primeiro valor na comparação. |
| `operand2` | corda, inteiro, boolean, matriz, objeto | Segundo valor na comparação. |

### <a name="return-value"></a>Valor devolvido

Se os óperas forem iguais, `true` é devolvido. Se os óperas forem diferentes, `false` é devolvido.

### <a name="example"></a>Exemplo

Pares de inteiros, cordas e booleanos são comparados.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

## <a name="not-equal-"></a>Não é igual!=

`operand1 != operand2`

Avalia se dois valores **não** são iguais.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | corda, inteiro, boolean, matriz, objeto | Primeiro valor na comparação. |
| `operand2` | corda, inteiro, boolean, matriz, objeto | Segundo valor na comparação. |

### <a name="return-value"></a>Valor devolvido

Se os óperas **não** forem iguais, `true` é devolvido. Se os óperas forem iguais, `false` é devolvido.

### <a name="example"></a>Exemplo

Pares de inteiros, cordas e booleanos são comparados.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

## <a name="equal-case-insensitive-"></a>Caso igual-insensível =~

`operand1 =~ operand2`

Ignora o caso para determinar se os dois valores são iguais.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1`  | cadeia (de carateres) | Primeira corda na comparação. |
| `operand2`  | string | Segunda corda na comparação. |

### <a name="return-value"></a>Valor devolvido

Se as cordas forem iguais, `true` é devolvida. Caso contrário, `false` será devolvido.

### <a name="example"></a>Exemplo

Compara cordas que usam letras mistas.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | false |

## <a name="not-equal-case-insensitive-"></a>Não igual caso-insensível!~

`operand1 !~ operand2`

Ignora o caso para determinar se os dois valores **não** são iguais.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | cadeia (de carateres) | Primeira corda na comparação. |
| `operand2` | string | Segunda corda na comparação. |

### <a name="return-value"></a>Valor devolvido

Se as cordas **não** forem iguais, `true` é devolvida. Caso contrário, `false` será devolvido.

### <a name="example"></a>Exemplo

Compara cordas que usam letras mistas.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | false |

## <a name="next-steps"></a>Passos seguintes

- Para criar um ficheiro Bicep, consulte [Tutorial: Crie e implemente primeiro o ficheiro Bicep do Gestor de Recursos Azure](bicep-tutorial-create-first-bicep.md).
- Para obter informações sobre como resolver erros do tipo Bicep, consulte [Qualquer função para Bicep](template-functions-any.md).
- Para comparar sintaxe para Bicep e JSON, consulte [Comparar JSON e Bicep para modelos](compare-template-syntax.md).
- Para exemplos de funções do modelo Bicep e ARM, consulte [as funções do modelo ARM](template-functions.md).
