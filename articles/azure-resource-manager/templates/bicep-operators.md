---
title: Operadores bicep
description: Descreve os operadores Bicep disponíveis para implementações do Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 4bf1005a11b1dcfea9f4b28d6bd3fa7c33e3278f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211334"
---
# <a name="bicep-operators"></a>Operadores bicep

Este artigo descreve os operadores Bicep que estão disponíveis quando cria um modelo Bicep e utiliza o Azure Resource Manager para implementar recursos. Os operadores são utilizados para calcular valores, comparar valores ou avaliar condições. Existem três tipos de operadores bicep: [comparação,](#comparison) [lógica](#logical)e [numérica.](#numeric)

## <a name="comparison"></a>Comparação

Os operadores de comparação comparam valores e `true` devolvem ou `false` .

| Operador | Nome | Descrição |
| ---- | ---- | ---- |
| `>=` | [Maior ou igual a](bicep-operators-comparison.md#greater-than-or-equal-) | Avalia se o primeiro valor é maior ou igual ao segundo valor. |
| `>`  | [Maior que](bicep-operators-comparison.md#greater-than-) | Avalia se o primeiro valor é maior do que o segundo valor. |
| `<=` | [Menor ou igual a](bicep-operators-comparison.md#less-than-or-equal-) | Avalia se o primeiro valor é inferior ou igual ao segundo valor. |
| `<`  | [Menos de](bicep-operators-comparison.md#less-than-) | Avalia se o primeiro valor é inferior ao segundo valor. |
| `==` | [Igual a](bicep-operators-comparison.md#equals-) | Avalia se dois valores são iguais. |
| `!=` | [Diferente](bicep-operators-comparison.md#not-equal-) | Avalia se dois valores **não** são iguais. |
| `=~` | [Caso igual-insensível](bicep-operators-comparison.md#equal-case-insensitive-) | Ignora o caso para determinar se dois valores são iguais. |
| `!~` | [Não igual caso-insensível](bicep-operators-comparison.md#not-equal-case-insensitive-) | Ignora o caso para determinar se dois valores **não** são iguais. |

## <a name="logical"></a>Lógico

Os operadores lógicos avaliam valores boolean, devolvem valores não nulos ou avaliam uma expressão condicional.

| Operador | Nome | Descrição |
| ---- | ---- | ---- |
| `&&` | [E](bicep-operators-logical.md#and-) | `true`Retorna se todos os valores forem verdadeiros. |
| `||`| [Ou](bicep-operators-logical.md#or-) | Devoluções `true` se um dos valores for verdadeiro. |
| `!` | [Não](bicep-operators-logical.md#not-) | Nega um valor booleano. |
| `??` | [Coalesce](bicep-operators-logical.md#coalesce-) | Devolve o primeiro valor não nulo. |
| `?` `:` | [Expressão condicional](bicep-operators-logical.md#conditional-expression--) | Avalia uma condição para verdadeiro ou falso e devolve um valor. |

## <a name="numeric"></a>Operador numérico

Os operadores numéricos usam inteiros para fazer cálculos e devolver valores inteiros.

| Operador | Nome | Descrição |
| ---- | ---- | ---- |
| `*` | [Multiplicar](bicep-operators-numeric.md#multiply-) | Multiplica dois inteiros. |
| `/` | [Dividir](bicep-operators-numeric.md#divide-) | Divide um inteiro por um inteiro. |
| `%` | [Módulo](bicep-operators-numeric.md#modulo-) | Divide um inteiro por um inteiro e devolve o restante. |
| `+` | [Adicionar](bicep-operators-numeric.md#add-) | Adiciona dois inteiros. |
| `-` | [Subtrair](bicep-operators-numeric.md#subtract--) | Subtrai um inteiro de um inteiro. |
| `-` | [Menos](bicep-operators-numeric.md#minus--) | Multiplica um inteiro por `-1` . |

> [!NOTE]
> Subtrair e menos utilizar o mesmo operador. A funcionalidade é diferente porque subtrair usa dois operands e menos usa um operand.

## <a name="next-steps"></a>Passos seguintes

- Para criar um ficheiro Bicep, consulte [Tutorial: Crie e implemente primeiro o ficheiro Bicep do Gestor de Recursos Azure](bicep-tutorial-create-first-bicep.md).
- Para obter informações sobre como resolver erros do tipo Bicep, consulte [Qualquer função para Bicep](template-functions-any.md).
- Para comparar sintaxe para Bicep e JSON, consulte [Comparar JSON e Bicep para modelos](compare-template-syntax.md).
- Para exemplos de funções do modelo Bicep e ARM, consulte [as funções do modelo ARM](template-functions.md).
