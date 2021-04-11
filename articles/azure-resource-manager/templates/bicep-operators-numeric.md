---
title: Operadores de numéricos bicep
description: Descreve operadores numéricos bicep que calculam valores.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211318"
---
# <a name="bicep-numeric-operators"></a>Operadores de numéricos bicep

Os operadores numéricos usam inteiros para fazer cálculos e devolver valores inteiros. Para executar os exemplos, utilize o Azure CLI ou o Azure PowerShell para [implementar um ficheiro Bicep](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operador | Name |
| ---- | ---- |
| `*` | [Multiplicar](#multiply-) |
| `/` | [Dividir](#divide-) |
| `%` | [Módulo](#modulo-) |
| `+` | [Adicionar](#add-) |
| `-` | [Subtrair](#subtract--) |
| `-` | [Menos](#minus--) |

> [!NOTE]
> Subtrair e menos utilizar o mesmo operador. A funcionalidade é diferente porque subtrair usa dois operands e menos usa um operand.

## <a name="multiply-"></a>Multiplique *

`operand1 * operand2`

Multiplica dois inteiros.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1`  | número inteiro | Número para multiplicar. |
| `operand2`  | número inteiro  | Multiplicador do número. |

### <a name="return-value"></a>Valor devolvido

A multiplicação devolve o produto como um inteiro.

### <a name="example"></a>Exemplo

Multiplicam-se dois inteiros e devolvem o produto.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `product` | número inteiro | 10 |

## <a name="divide-"></a>Dividir /

`operand1 / operand2`

Divide um inteiro por um inteiro.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | número inteiro | Inteiro que está dividido. |
| `operand2` | número inteiro | Inteiro que é usado para divisão. Não pode ser zero. |

### <a name="return-value"></a>Valor devolvido

A divisão devolve o quociente como um inteiro.

### <a name="example"></a>Exemplo

Dois inteiros são divididos e devolvem o quociente.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `quotient` | número inteiro | 5 |

## <a name="modulo-"></a>Modulo %

`operand1 % operand2`

Divide um inteiro por um inteiro e devolve o restante.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1`  | número inteiro  | O inteiro que está dividido. |
| `operand2`  | número inteiro  | O inteiro que é usado para a divisão. Não pode ser 0. |

### <a name="return-value"></a>Valor devolvido

O restante é devolvido como um inteiro. Se a divisão não produzir um resto, 0 será devolvido.

### <a name="example"></a>Exemplo

Dois pares de inteiros são divididos e devolvem os restantes.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `remainder` | número inteiro | 1 |
| `zeroRemainder` | número inteiro | 0 |

## <a name="add-"></a>Adicionar +

`operand1 + operand2`

Adiciona dois inteiros.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | número inteiro | Número a adicionar. |
| `operand2` | número inteiro | Número que é adicionado a um número. |

### <a name="return-value"></a>Valor devolvido

A adição devolve a soma como um inteiro.

### <a name="example"></a>Exemplo

Dois inteiros são adicionados e devolvem a soma.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `sum` | número inteiro | 12 |

## <a name="subtract--"></a>Subtrair -

`operand1 - operand2`

Subtrai um inteiro de um inteiro.

### <a name="operands"></a>Óperas

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `operand1` | número inteiro | Um número maior que é subtraído. |
| `operand2` | número inteiro | Número que é subtraído do número maior. |

### <a name="return-value"></a>Valor devolvido

A subtração devolve a diferença como um inteiro.

### <a name="example"></a>Exemplo

Um inteiro é subtraído e devolve a diferença.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `difference` | número inteiro | 6 |

## <a name="minus--"></a>Menos -

`-integerValue`

Multiplica um inteiro por `-1` .

### <a name="operand"></a>Ópera

| Ópera | Tipo | Description |
| ---- | ---- | ---- |
| `integerValue` | número inteiro | Inteiro multiplicado por `-1` . |

### <a name="return-value"></a>Valor devolvido

Um inteiro é multiplicado por `-1` . Um número inteiro positivo devolve um número inteiro negativo e um número inteiro negativo devolve um número inteiro positivo. Os valores podem ser embrulhados com parênteses.

### <a name="example"></a>Exemplo

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

Saída a partir do exemplo:

| Nome | Tipo | Valor |
| ---- | ---- | ---- |
| `startedPositive` | número inteiro | -10 |
| `startedNegative` | número inteiro | 20 |

## <a name="next-steps"></a>Passos seguintes

- Para criar um ficheiro Bicep, consulte [Tutorial: Crie e implemente primeiro o ficheiro Bicep do Gestor de Recursos Azure](bicep-tutorial-create-first-bicep.md).
- Para obter informações sobre como resolver erros do tipo Bicep, consulte [Qualquer função para Bicep](template-functions-any.md).
- Para comparar sintaxe para Bicep e JSON, consulte [Comparar JSON e Bicep para modelos](compare-template-syntax.md).
- Para exemplos de funções do modelo Bicep e ARM, consulte [as funções do modelo ARM](template-functions.md).
