---
title: Criar funções lógicas de definição de UI
description: Descreve as funções para executar operações lógicas.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87098181"
---
# <a name="createuidefinition-logical-functions"></a>Funções lógicas CreateUiDefinition

Estas funções podem ser utilizadas em expressões condicionais. Algumas funções podem não suportar todos os tipos de dados JSON.

## <a name="and"></a>e

Devoluções `true` se todos os parâmetros avaliarem `true` . Esta função suporta dois ou mais parâmetros apenas do tipo boolean.

O seguinte exemplo `true` regressa:

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

O seguinte exemplo `false` regressa:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>coalesce

Devolve o valor do primeiro parâmetro não nulo. Esta função suporta todos os tipos de dados JSON.

Assuma `element1` e `element2` é indefinido. O seguinte exemplo `"Contoso"` regressa:

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

Esta função é especialmente útil no contexto da invocação opcional que acontece devido à ação do utilizador após a carga da página. Um exemplo é se os constrangimentos colocados num campo na UI dependem do valor atualmente selecionado de outro campo, **inicialmente não visível.** Neste caso, `coalesce()` pode ser utilizado para permitir que a função seja sintaticamente válida no tempo de carregamento da página, tendo o efeito desejado quando o utilizador interage com o campo.

Considere `DropDown` isto, que permite ao utilizador escolher entre vários tipos de bases de dados diferentes:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Para condicionar a ação de outro campo sobre o valor atual escolhido deste campo, `coalesce()` utilize, como mostrado aqui:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Isto é necessário porque `databaseType` inicialmente não é visível e, portanto, não tem valor. Isto faz com que toda a expressão não avalie corretamente.

## <a name="equals"></a>equals

Devoluções `true` se ambos os parâmetros tiverem o mesmo tipo e valor. Esta função suporta todos os tipos de dados JSON.

O seguinte exemplo `true` regressa:

```json
"[equals(0, 0)]"
```

O seguinte exemplo `true` regressa:

```json
"[equals('web', 'web')]"
```

O seguinte exemplo `false` regressa:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

Devoluções `true` se o primeiro parâmetro for estritamente maior do que o segundo parâmetro. Esta função suporta parâmetros apenas de número de tipo e corda.

O seguinte exemplo `false` regressa:

```json
"[greater(1, 2)]"
```

O seguinte exemplo `true` regressa:

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

Devoluções `true` se o primeiro parâmetro for maior ou igual ao segundo parâmetro. Esta função suporta parâmetros apenas de número de tipo e corda.

O seguinte exemplo `true` regressa:

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

Devolve um valor baseado no facto de uma condição ser verdadeira ou falsa. O primeiro parâmetro é a condição para testar. O segundo parâmetro é o valor para devolver se a condição for verdadeira. O terceiro parâmetro é o valor a devolver se a condição for falsa.

As seguintes devoluções de `yes` amostras .

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

Devoluções `true` se o primeiro parâmetro for estritamente inferior ao segundo parâmetro. Esta função suporta parâmetros apenas de número de tipo e corda.

O seguinte exemplo `true` regressa:

```json
"[less(1, 2)]"
```

O seguinte exemplo `false` regressa:

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

Devoluções `true` se o primeiro parâmetro for inferior ou igual ao segundo parâmetro. Esta função suporta parâmetros apenas de número de tipo e corda.

O seguinte exemplo `true` regressa:

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

Retorna `true` se o parâmetro avaliar a `false` . Esta função suporta parâmetros apenas do tipo Boolean.

O seguinte exemplo `true` regressa:

```json
"[not(false)]"
```

O seguinte exemplo `false` regressa:

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>ou

Devoluções `true` se pelo menos um dos parâmetros avaliar a `true` . Esta função suporta apenas dois ou mais parâmetros do tipo Boolean.

O seguinte exemplo `true` regressa:

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

O seguinte exemplo `true` regressa:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução ao Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../management/overview.md).
